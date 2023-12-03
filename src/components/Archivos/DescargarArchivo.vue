<template>
  <div class="section-descargar">
    <b-loading :active="cargando"></b-loading>
    <div class="content-wrapper" v-show="archivo.nombre">
      <div class="title-wrapper">
        <p class="title">Listos para descargar</p>
        <p class="subtitle">{{ archivo.nombre }}</p>
      </div>
      <div class="button-wrapper">
        <b-button @click="descargar()" type="is-success" icon-right="download">
          Descargar
        </b-button>
      </div>
      <div class="notification-wrapper">
        <b-notification type="is-info" :closable="false" has-icon>
          <p>
            No olvide respaldar el archivo, ya que el enlace tiene una fecha de expiración. Además, la mayoría de veces el reenvío del archivo tiene un costo.
          </p>
        </b-notification>
      </div>
    </div>
    <div class="invalid-link" v-show="!archivo.nombre && !cargando">
      <p class="title">Enlace inválido</p>
      <p class="subtitle">
        Parece que el enlace que has seguido ha expirado, es inválido o el archivo al que apuntaba ha sido eliminado.
      </p>
    </div>
  </div>
</template>

<style scoped>
.section-descargar {
  padding: 2em;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  margin-bottom: -100px;
  margin-top: -50px;
}

.content-wrapper {
  max-width: 600px;
  text-align: center;
}

.title-wrapper,
.button-wrapper,
.notification-wrapper {
  margin-bottom: 1em;
}

.title {
  font-size: 2em;
  font-weight: bold;
}

.subtitle {
  font-size: 1.5em;
  margin-bottom: 0.5em;
}

.b-button {
  margin-top: 0.5em;
}

.notification-wrapper {
  margin-top: 1em;
}

.invalid-link {
  text-align: center;
}

/* Ajustes adicionales para el estilo de la notificación de Bulma */
.b-notification {
  padding: 1em;
  border-radius: 0.5em;
  font-size: 1em;
}
</style>


<script>
import { doc, getDoc, updateDoc, } from '@firebase/firestore';
import FirebaseService from '../../services/FirebaseService';
import { getDownloadURL, ref } from '@firebase/storage';
export default {
  data: () => ({
    archivo: {},
    referenciaDescargas: {},
    descargas: { descargas: {} },
    cargando: false,
  }),
  async mounted() {
    this.cargando = true;
    const id = this.$route.params.id;
    const referenciaAlDocumento = doc(await FirebaseService.obtenerFirestore(), "archivos", id);
    const referenciaDescargas = doc(await FirebaseService.obtenerFirestore(), "descargasArchivos", id);
    this.referenciaDescargas = referenciaDescargas;
    const instantaneaDocumento = await getDoc(referenciaAlDocumento);
    const instantaneaDescargas = await getDoc(referenciaDescargas);
    this.cargando = false;
    if (instantaneaDocumento.exists()) {
      this.archivo = instantaneaDocumento.data();
    }
    if (instantaneaDescargas.exists()) {
      this.descargas = instantaneaDescargas.data();
    }
  },
  methods: {
    async descargar() {
      this.cargando = true;
      this.descargas.descargas.push(new Date().getTime());
      await updateDoc(this.referenciaDescargas, { descargas: this.descargas.descargas });
      try {
        const url = await getDownloadURL(ref(await FirebaseService.obtenerStorage(), this.archivo.uuid + "/" + this.archivo.nombre))
        window.location.href = url;
      } catch (error) {
        this.$buefy.toast.open({
          message: "Error descargando archivo. Tal vez no tiene los permisos suficientes. Error: " + error.message,
          type: 'is-danger'
        });
      } finally {
        this.cargando = false;
      }
    }
  }
}
</script>
