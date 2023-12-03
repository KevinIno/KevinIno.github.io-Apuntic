<template>
  <div class="section_subir">
    <div class="columns">
      <div class="column has-text-centered">
        <b-button
          type="is-success"
          icon-right="format-list-numbered"
          @click="verArchivos()"
          :disabled="estaSubiendo"
          class="boton-ver-archivos"
        >
          Ver archivos
        </b-button>
      </div>
    </div>
    <div class="columns">
      <div class="column is-half is-offset-one-quarter">
        <b-field class="upload-field">
          <b-upload
            :disabled="estaSubiendo"
            @change.native="onArchivosSeleccionados"
            v-model="archivo"
            drag-drop
            expanded
          >
            <section class="upload-section">
              <div class="content has-text-centered">
                <p>
                  <b-icon icon="upload" size="is-large"></b-icon>
                </p>
                <p class="upload-text">Clic aquí para buscar el archivo</p>
              </div>
            </section>
          </b-upload>
        </b-field>
        <span v-if="archivo" class="tag is-primary archivo-tag">
          {{ archivo.name }}
        </span>
        <b-notification
          v-show="estaPausado"
          type="is-danger"
          class="notificacion-pausado"
          :closable="false"
        >
          Pausado
        </b-notification>
        <b-progress
          v-show="estaSubiendo"
          :value="progreso"
          type="is-success"
          show-value
          format="percent"
          class="barra-progreso"
        ></b-progress>
        <b-button
          :disabled="estaSubiendo"
          :loading="estaSubiendo"
          type="is-success"
          class="boton-subir"
          @click="subir"
        >
          Subir
        </b-button>
      </div>
    </div>
  </div>
</template>
<script>
import { ref, uploadBytesResumable } from 'firebase/storage';
import FirebaseService from "../../services/FirebaseService.js";
import { addDoc, doc, setDoc } from '@firebase/firestore';
import { v4 as uuidv4 } from 'uuid';
import crypto from 'crypto';

export default {
  data: () => ({
    archivo: null,
    estaSubiendo: false,
    estaPausado: false,
    progreso: 0,
  }),

  mounted() {
    window.addEventListener("beforeunload", (evento) => {
      if (this.estaSubiendo) {
        evento.preventDefault();
        evento.returnValue = "";
        return "";
      }
    });
  },

  methods: {
    onArchivosSeleccionados() {
      this.subir();
    },

    verArchivos() {
      this.$router.push({
        name: "VerArchivos",
      });
    },

    async subir() {
      if (!this.archivo) {
        return;
      }

      try {
        // Lee el contenido del archivo
        const buffer = await this.archivo.arrayBuffer();
        const archivoBuffer = Buffer.from(buffer);

        // Genera una clave de encriptación única
        const claveEncriptacion = crypto.randomBytes(16); // 128 bits

        // Cifra el contenido del archivo usando la clave
        const cipher = crypto.createCipher('aes-128-cbc', claveEncriptacion);
        const archivoEncriptado = Buffer.concat([cipher.update(archivoBuffer), cipher.final()]);

        const nombre = this.archivo.name;
        const uuid = uuidv4();
        const storage = await FirebaseService.obtenerStorage();
        const tarea = uploadBytesResumable(ref(storage, uuid + "/" + nombre), archivoEncriptado);

        this.estaSubiendo = true;
        this.estaPausado = false;

        tarea.on("state_changed",
          (snapshot) => {
            this.progreso = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
            if (snapshot.state === "paused") {
              this.estaPausado = true;
            } else if (snapshot.state === "running") {
              this.estaPausado = false;
            }
          },
          (error) => {
            this.$buefy.toast.open({
              message: "Error subiendo archivo: " + error.message,
              type: 'is-danger'
            });
          },
          async () => {
            this.$buefy.toast.open({
              message: "Archivo subido. Guardando detalles...",
              type: 'is-info'
            });

            const documentoRecienCreado = await addDoc(await FirebaseService.obtenerColeccionArchivos(), {
              uuid,
              nombre,
              fecha: new Date().getTime(),
            });

            await setDoc(doc(await FirebaseService.obtenerFirestore(), "descargasArchivos", documentoRecienCreado.id), { descargas: [] });

            // Almacena la clave de encriptación en la base de datos
            await setDoc(doc(await FirebaseService.obtenerFirestore(), "clavesEncriptacion", documentoRecienCreado.id), {
              clave: claveEncriptacion.toString('base64'),
            });

            this.$buefy.toast.open({
              message: "Subida terminada correctamente",
              type: 'is-success'
            });

            this.estaSubiendo = false;
            this.estaPausado = false;
            this.archivo = null;
            this.progreso = 0;
          }
        );
      } catch (error) {
        console.error('Error:', error);
        this.$buefy.toast.open({
          message: "Error subiendo archivo: " + error.message,
          type: 'is-danger'
        });
        this.estaSubiendo = false;
        this.estaPausado = false;
      }
    },
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@700&display=swap');

.form-title,
.custom-label {
  font-family: 'Montserrat', sans-serif;
  font-weight: 700;
  /* Peso de la fuente para que sea bold */
}
.section_subir {
  padding: 20px;
  margin-bottom: 190px;
}

.boton-ver-archivos {
  margin-bottom: 20px;
}

.upload-field {
  margin-top: 20px;
  border: 2px dashed #ddd;
  padding: 20px;
  border-radius: 8px;
}

.upload-section {
  padding: 20px;
}

.upload-text {
  margin-top: 10px;
  font-weight: bold;
}

.archivo-tag {
  display: block;
  margin-top: 10px;
}

.notificacion-pausado {
  margin-top: 10px;
}

.barra-progreso {
  margin-top: 20px;
}

.boton-subir {
  margin-top: 20px;
  width: 100%;
}
</style>