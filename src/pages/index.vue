<template>
  <div id="container">

    <div>
      <Brander />
    </div>

    <form id="papers_form" action="">

      <input type="number" v-model="paper_1" placeholder="Valor del activo" />
      <Selector ref="selector1"/>

      <input type="number" v-model="paper_2" placeholder="Valor del activo" />
      <Selector ref="selector2"/>

      <input type="number" v-model="fixed_result" placeholder="" disabled />
      <br>
      <input type="number" v-model="variable_result" placeholder="" disabed/>
      <br>
      <div id="buttons_row">
      <button @click.prevent="clearInputs">Limpiar</button>
      <button @click.prevent="calculate">Calcular</button>
    </div>


    </form>

    <div>
      <Footer />
    </div>
  </div>
</template>

<script>
import Brander from '../../public/components/brander.vue';
import Selector from '../../public/components/selector.vue';
import Footer from '../../public/components/footer.vue';


export default {
  components: {
    Brander,
    Selector,
    Footer
  },
  data() {
    return {
      paper_1: null,
      paper_2: null,
      fixed_result: null,
      variable_result: null,
    };
  },
  methods: {
    calculate() {
      let fixedSum = 0;
      let variableSum = 0;

      for (let i = 1; i <= 2; i++) {
        const selector = this.$refs['selector' + i];
        if (selector.selected === 'fixed') {
          fixedSum += this['paper_' + i];
        } else if (selector.selected === 'variable') {
          variableSum += this['paper_' + i];
        }
      }

      this.fixed_result = fixedSum;
      this.variable_result = variableSum;
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

#buttons_row {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
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