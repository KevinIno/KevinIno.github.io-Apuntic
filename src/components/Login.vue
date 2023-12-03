<template>
  <section class="section full-height">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-md-6">
          <form @submit.prevent="iniciarSesion" class="custom-form">
            <h1 class="form-title">Iniciar sesión</h1>
            <div class="form-group">
              <label for="correo" class="custom-label">Correo</label>
              <input v-model="correo" type="email" id="correo" class="custom-input" placeholder="Coloque su correo"
                :loading="cargando" required autofocus />
            </div>
            <div class="form-group">
              <label for="contrasena" class="custom-label">Contraseña</label>
              <input v-model="palabraSecreta" type="password" id="contrasena" class="custom-input"
                placeholder="Coloque contraseña" :loading="cargando" required />
            </div>
            <b-button :loading="cargando" class="is-success-login" @click="iniciarSesion()">Iniciar sesión</b-button>
          </form>
        </div>
      </div>
    </div>
  </section>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@700&display=swap');

.form-title,
.custom-label {
  font-family: 'Montserrat', sans-serif;
  font-weight: 700;
  /* Peso de la fuente para que sea bold */
}

.section.full-height {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: gray;
}

.container {
  width: 100%;
}

/* Asegúrate de que tu formulario no tenga margen superior que lo desplace hacia abajo */
.custom-form {
  margin-top: 0;
  /* Resto de tus estilos para .custom-form */
}

/* Centra tu formulario en la pantalla con flexbox */
.row.justify-content-center {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

.col-md-6 {
  flex: 0 0 100%;
  /* Hace que el formulario tome el 100% del ancho de su contenedor en dispositivos medianos */
  max-width: 100%;
  /* Hace que el formulario tome el 100% del ancho de su contenedor */
  display: flex;
  justify-content: center;
}


.custom-form {
  background-color: #f9f9f9;
  padding: 20px;
  border: none;
  border-radius: 8px;
  margin-top: 20px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  max-width: 400px;
  width: 100%;
}

.is-success-login {
  background-color: #48c78e;
  color: white;
  width: 100%;
}

.form-title {
  font-size: 24px;
  margin-bottom: 20px;
  text-align: center;
  color: #333;
}

.custom-label {
  font-size: 18px;
  font-weight: bold;
  color: #333;
  display: block;
  margin-bottom: 5px;
}

.custom-input {
  width: 100%;
  padding: 10px;
  margin-bottom: 15px;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1);
}

.container_login {
  flex-grow: 1;
}

.row.justify-content-center {
  display: flex;
  justify-content: center;
}

.col-md-6 {
  flex: 0 0 50%;
  max-width: 50%;
}
</style>


<script>
import { getAuth, signInWithEmailAndPassword } from "firebase/auth";
import FirebaseService from "../services/FirebaseService";
export default {
  data: () => ({
    correo: "",
    palabraSecreta: "",
    cargando: false,
  }),
  methods: {
    async iniciarSesion() {
      if (!this.correo || !this.palabraSecreta) {
        this.$buefy.toast.open("Completa los campos");
        return;
      }
      if (!this.correo.includes("@")) {
        this.$buefy.toast.open("Escribe un correo válido");
        return;
      }
      this.cargando = true;
      FirebaseService.obtenerApp();
      const auth = getAuth();
      try {
        await signInWithEmailAndPassword(
          auth,
          this.correo,
          this.palabraSecreta
        );
        this.$buefy.toast.open("Bienvenido");
      } catch (error) {
        this.$buefy.dialog.alert({
          title: "Error",
          message:
            "Error iniciando sesión. Verifica tu correo y contraseña. El error es: " +
            error.message,
          type: "is-danger",
          hasIcon: true,
          icon: "alert",
        });
      } finally {
        this.cargando = false;
      }
    },
  },
};
</script>