<template>
  <div id="container">
    <div>
      <Brander />
    </div>

    <div id="content">
      <div id="input_wrapper">
        <input type="number" v-model.number="newItem" placeholder="Nuevo ítem" />
        <button @click="addItem(1)">fixed</button>
        <button @click="addItem(2)">viariable</button>
      </div>

      <div id="item_list">
        <div v-for="item in items" :key="item.id" class="item">
          <span>{{ item.value }}</span>
          <button @click="deleteItem(item.id)">-</button>
        </div>
      </div>

      <div id="total_wrapper">
        <!-- <label>Total:</label> -->
        <input type="number" class="total_input" v-model="total" disabled />
        <input type="number" class="total_input" v-model="totalVariable" disabled />

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
      total: 0,
      totalVariable: 0,
    };
  },
  methods: {
    addItem(cateogry) {

      if(cateogry == 1){ // Fixed
        if (this.newItem !== null) {
          this.items.push({ id: Date.now(), value: this.newItem+" fixed" });
          this.total += this.newItem;
          this.newItem = null;
        }
      } else { // Variavles
        if (this.newItem !== null) {
          this.items.push({ id: Date.now(), value: this.newItem+" variable" });
          this.totalVariable += this.newItem;
          this.newItem = null;
        }
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
@import url('https://fonts.googleapis.com/css2?family=Kode+Mono:wght@400..700&family=Montserrat:ital,wght@0,100..900;1,100..900&display=swap');

#container {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: 1fr;
  align-items: center;
  justify-items: center;
  height: 100%;
  background: rgb(246,151,218);
  background: linear-gradient(312deg, rgba(246,151,218,1) 18%, rgba(244,237,135,1) 62%);
  font-family: "Montserrat", sans-serif;
  height: 100vh;
  overflow: hidden;
}

#content {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

#input_wrapper {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  width: 100%;
}

input[type="number"] {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-right: 10px;
  flex: 1;
}

#input_wrapper button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: rgba(0, 0, 0, 0.1);
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
  margin: 0px 10px;

}

button:hover {
  background-color: #0056b3;
}

#item_list {
  width: 100%;
}

#item_list button{
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: rgba(0, 0, 0, 0.1);
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
  margin: 10px 0px;
}

.item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  text-indent: 10px;
}

#total_wrapper {
  display: flex;
  flex-direction: row;
  margin-top: 20px;
  width: 100%;
}

#total_wrapper .total_input{
  width: 100px;
}

label {
  font-weight: bold;
}

input[type="number"][disabled] {
  background-color: #f0f0f0;
  border: none;
}

@media (max-width: 480px) {
  #container {
    display: flex;
    flex-direction: column;
    margin-top: unset;
  }
}
</style>