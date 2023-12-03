<template>
    <section class="section">
        <div class="container">
            <div class="row justify-content-center">
                <div class="col-md-6">
                    <form @submit.prevent="registerUser" class="custom-form">
                        <h1 class="form-title">Registro</h1>
                        <div class="form-group">
                            <label for="email" class="custom-label">Correo Electrónico</label>
                            <input type="email" id="email" v-model="email" class="custom-input" placeholder="tu@correo.com"
                                required autofocus />
                        </div>
                        <div class="form-group">
                            <label for="password" class="custom-label">Contraseña</label>
                            <input type="password" id="password" v-model="password" class="custom-input"
                                placeholder="Tu contraseña segura" required />
                        </div>
                        <button type="submit" class="custom-button is-success">Registrarse</button>
                    </form>
                </div>
            </div>
        </div>
    </section>
</template>
  
<script>
import firebase from '@/firebaseConfig';

export default {
    name: 'Registro',
    data() {
        return {
            email: '',
            password: ''
        };
    },
    methods: {
        async registerUser() {
            try {
                const { user } = await firebase.auth().createUserWithEmailAndPassword(this.email, this.password);
                // Usuario registrado con éxito
                this.$router.push('/home'); // Redirecciona al usuario a la página de inicio o donde desees
            } catch (error) {
                console.error("Error al registrar al usuario:", error.message);
                alert("Error al registrar: " + error.message); // Muestra un mensaje de error
            }
        }
    }
};
</script>
  
<style scoped>
.section {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.container {
    max-width: 500px;
    width: 100%;
}

.row.justify-content-center {
    display: flex;
    justify-content: center;
}

.custom-form {
    background-color: #f9f9f9;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.form-title {
    margin-bottom: 20px;
    text-align: center;
}

.custom-label {
    display: block;
    margin-bottom: 5px;
}

.custom-input {
    width: 100%;
    padding: 10px;
    margin-bottom: 15px;
    border: 1px solid #ccc;
    border-radius: 4px;
}

.custom-button.is-success {
    width: 100%;
    padding: 10px;
    border: none;
    border-radius: 5px;
    background-color: #4CAF50;
    color: white;
    cursor: pointer;
}

.custom-button.is-success:hover {
    background-color: #45a049;
}
</style>
  