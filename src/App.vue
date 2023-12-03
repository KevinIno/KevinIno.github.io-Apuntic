<template>
  <div>
    <div class="container">
      <router-view />
    </div>
    <footer class="footer">
      <div class="footer-content">
        <div class="footer-section">
          <img src="./assets/notitas2.png" alt="NOTITAS-APUNTIC Logo" class="logo-image" />
        </div>
        <div class="footer-section">
          <p class="footer-title">Acerca de</p>
          <p>Plataforma Academica en el intercambio de apuntes de la Universidad Catolica de Santa Maria</p>
        </div>
        <div class="footer-section">
          <p class="footer-title">Contacto</p>
          <p>info@notitasapuntic.com</p>
        </div>
        <div class="footer-section">
          <p class="footer-title">Síguenos</p>
          <p>
            <a href="#">Facebook</a> | <a href="#">X</a> | <a href="#">Instagram</a>
          </p>
          <br/>
          <b-icon icon="heart" type="is-danger"></b-icon>
          <br />
        </div>
      </div>
    </footer>
  </div>
</template>

<style scoped>

@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@700&display=swap');

.form-title,
.custom-label {
  font-family: 'Montserrat', sans-serif;
  font-weight: 700;
  /* Peso de la fuente para que sea bold */
}
.footer {
  background-color: #f5f5f5;
  padding: 20px 0;
  box-shadow: 0 -2px 4px rgba(0, 0, 0, 0.1);
}

.footer-content {
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.footer-section {
  flex-basis: 20%;
  text-align: center;
}

.logo-image {
  width: 100px;
  height: auto;
}

.footer-brand {
  font-size: 18px;
  font-weight: bold;
  margin-top: 10px;
}

.footer-title {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 5px;
}

.footer a {
  color: inherit;
  text-decoration: none;
}

.footer a:hover {
  text-decoration: underline;
}

/* Estilos adicionales según sea necesario */
</style>

<script>
export default {
  name: 'app',
}
</script>