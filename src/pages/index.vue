<template>
  <div id="container">
    <div>
      <Brander />
    </div>

    <div id="content">
      <div id="input_wrapper">
        <input type="number" v-model.number="newItem" placeholder="Nuevo ítem" />
        <button @click="addItem">Sumar</button>
      </div>

      <div id="item_list">
        <div v-for="item in items" :key="item.id" class="item">
          <span>{{ item.value }}</span>
          <button @click="deleteItem(item.id)">Eliminar</button>
        </div>
      </div>

      <div id="total_wrapper">
        <label>Total:</label>
        <input type="number" v-model="total" disabled />
      </div>
    </div>

    <div>
      <Footer />
    </div>
  </div>
</template>

<script>
import Brander from '../../public/components/brander.vue';
import Footer from '../../public/components/footer.vue';

export default {
  components: {
    Brander,
    Footer
  },
  data() {
    return {
      newItem: null,
      items: [],
      total: 0
    };
  },
  methods: {
    addItem() {
      if (this.newItem !== null) {
        this.items.push({ id: Date.now(), value: this.newItem });
        this.total += this.newItem;
        this.newItem = null;
      }
    },
    deleteItem(id) {
      const item = this.items.find(item => item.id === id);
      if (item) {
        this.total -= item.value;
        this.items = this.items.filter(item => item.id !== id);
      }
    }
  }
};
</script>

<style scoped>
#container {
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

#content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

#total_wrapper {
  margin-top: 20px;
}

.todo-item {
  margin-bottom: 10px;
}

@media (max-width: 480px) {
  #container {
    display: flex;
    flex-direction: column;
    margin-top: unset;
  }
}
</style>
