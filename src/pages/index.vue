<template>
  <div id="container">

    <div>
      <Brander />
    </div>

    <form id="papers_form" action="">

      <input type="number" v-model="capital" placeholder="Ingrese el capital" />
      <Switcher />

    </form>

    <div>
      <Footer />
    </div>
  </div>
</template>

<script>
import Brander from '../../public/components/brander.vue';
import Switcher from '../../public/components/switcher.vue';
import Footer from '../../public/components/footer.vue';


export default {
  components: {
    Brander,
    Switcher,
    Footer
  },
  data() {
    return {
      inputData: ''
    };
  },
  methods: {
    saveToLocalStorage() {
      if (this.inputData.trim() !== '') {
        localStorage.setItem('userData', this.inputData);
        this.inputData = ''; // Limpiar el input después de guardar
        alert('Datos guardados en el almacenamiento local.');
      } else {
        alert('Ingrese datos antes de guardar.');
      }
    }
  }
};
</script>

<style scoped>

@import url('https://fonts.googleapis.com/css2?family=Nunito:wght@400;700&display=swap');

#container{
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: 1fr;
  align-items: center;
  justify-items: center;
  height: 100vh;
  background: rgb(246,151,218);
  background: linear-gradient(312deg, rgba(246,151,218,1) 18%, rgba(244,237,135,1) 62%);
  font-family: Nunito;
}

#papers_form {
  display: grid;
  grid-template-columns: 200px 100px;
  gap: 20px;
  margin-top: 30px;
  align-items: center;
}

a {
  text-decoration: none;
  font-size: 18px;
  color: #fff;
}


#papers_form h2, h3{
  color: #484848;
  margin: 5px;
}

label {
  font-size: 16px;
  color: #484848;
  margin-bottom: 8px;
}

input {
  font-size: 16px;
  padding: 10px;
  border: 1px solid #484848;
  border-radius: 4px;
  width: 100%;
  box-sizing: border-box;
}

input[disabled] {
  background-color: #f5f5f5;
}

button {
  font-size: 16px;
  padding: 10px 20px;
  background-color: #C4368C;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #484848;
}

@media (max-width: 480px) {
  #container {
    display: flex;
    flex-direction: column;
    margin-top: unset;
  }

}


</style>