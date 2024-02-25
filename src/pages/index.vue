<template>
  <div id="container">
    <div>
      <Brander />
    </div>
  </div>

  <div id="content">
    content
  </div>

  <div>
    <Footer />
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
      // Variables de tu componente
    };
  },
  methods: {
    // Métodos de tu componente
  },
  computed: {
    // Propiedades computadas de tu componente
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
  height: 100%;
  background: rgb(246,151,218);
  background: linear-gradient(312deg, rgba(246,151,218,1) 18%, rgba(244,237,135,1) 62%);
  font-family: Nunito;
  height: 100vh;
  overflow: hidden;
}

@media (max-width: 480px) {
  #container {
    display: flex;
    flex-direction: column;
    margin-top: unset;
  }

}


</style>