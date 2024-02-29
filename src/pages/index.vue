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
        <div v-for="item in sortedItems" :key="item.id" class="item">
          <span>{{ item.value }} {{ item.type }}</span>
          <button @click="deleteItem(item.id)">-</button>
        </div>
      </div>

      <div id="total_wrapper">
        <div id="type_totals">
          <div class="type_totals_wrappers" id="fixed_total">
            <label for="total" style="font-weight: normal;">Fixed income</label>
            <input id="totalVariable" type="text" class="total_input" :value="totalFixedPercentage" disabled />
          </div>

          <div class="type_totals_wrappers" id="variable_total">
            <label for="totalVariable">Variable income</label>
            <input id="totalVariable" type="text" class="total_input" :value="totalVariablePercentage" disabled />
          </div>
        </div>

        <div class="type_totals_wrappers">
          <label for="totalVariable">Total income</label>
          <input type="number" class="total_input" id="max_total" v-model="totalMax" disabled />
        </div>
      </div>

      <div id="saving_wrapper">
        <button @click="generateToken()">Generar token</button>
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

// Agregar para mandar por whatsapp

export default {
  components: {
    Brander,
    Footer
  },
  data() {
    return {
      newItem: null,
      items: [],
      itemsVariable: [],
      total: 0,
      totalVariable: 0,
      totalMax: 0,
      variablePercentage: 50,
      fixedPercentage: 50
    };
  },
  methods: {
    fixedSum(){
      return this.total = this.items.reduce((total, item) => total + parseInt(item.value), 0);
    },
    variableSum(){
      return this.totalVariable = this.itemsVariable.reduce((total, item) => total + parseInt(item.value), 0);
    },
    maxSum(){
      const fixedTotal = this.items.reduce((total, item) => total + parseInt(item.value), 0);
      const variableTotal = this.itemsVariable.reduce((total, item) => total + parseInt(item.value), 0);
      return this.totalMax = fixedTotal + variableTotal;
    },
    addItem(cateogry) {

      if(cateogry == 1){ // Fixed
        if (this.newItem !== null) {
          this.items.push({ id: Date.now(), value: this.newItem, type: "fixed" });
          this.newItem = null;
          this.fixedSum();

        }
      } else { // Variavles
        if (this.newItem !== null) {
          this.itemsVariable.push({ id: Date.now(), value: this.newItem, type: "variable" });
          this.newItem = null;
          this.variableSum();
        }
      }

      this.maxSum();
    },
    deleteItem(id) {

      let index = this.items.findIndex(item => item.id === id);
      if (index !== -1) {
        this.total -= parseInt(this.items[index].value);
        this.totalMax -= parseInt(this.items[index].value);
        this.items.splice(index, 1);
      }

      index = this.itemsVariable.findIndex(item => item.id === id);
      if (index !== -1) {
        this.totalVariable -= parseInt(this.itemsVariable[index].value);
        this.totalMax -= parseInt(this.itemsVariable[index].value);
        this.itemsVariable.splice(index, 1);
      }
     

      this.fixedSum();
      this.variableSum();

    },
    generateToken(){
      if(this.items[0] || this.itemsVariable[0]){
        var allItems = [...this.items, ...this.itemsVariable].sort((a, b) => a.id - b.id);
        
        var itemJoined = allItems.map(item => {
          return item.id + "," + item.value + "," + (item.type === "fixed" ? "f" : "v");
        }).join("zzz");
      }
    }
  },
  computed: {
    sortedItems() {
      const allItems = [...this.items, ...this.itemsVariable];
      return allItems.sort((a, b) => a.id - b.id);
    },
    totalFixedPercentage() {
      this.fixedPercentage = Math.round(100 / this.totalMax * this.total);
      return `${this.total} - ${this.fixedPercentage}%`;
    },
    totalVariablePercentage() {
      this.variablePercentage = Math.round(100 / this.totalMax * this.totalVariable);
      return `${this.totalVariable} - ${this.variablePercentage}%`;
    },
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

#type_totals_wrappers input{
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-right: 10px;
  flex: 1;
}

button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: rgba(0, 0, 0, 0.1);
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

#saving_wrapper button{
  margin: 10px 0px;
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
  height: 36px;
}

#total_wrapper {
  display: flex;
  flex-direction: column;
  margin-top: 20px;
  width: 100%;
}

#total_wrapper .total_input{
  width: 100px;
}

#total_wrapper #type_totals{
  display: grid;
  grid-template-columns: 1fr 1fr;

  padding: 20px 0px;
}

#total_wrapper .type_totals_wrappers{
  display: flex;
  flex-direction: column;
  font-weight: unset;
  font-family: "Montserrat", sans-serif;
  text-indent: 5px;
  line-height: 22px;
}

.type_totals_wrappers #max_total{
  width: 266px;
}


input[type="number"][disabled] {
  background-color: #f0f0f0;
  border: none;
}

input{
  height: 33px;
  border-radius: 5px;
  border: none;
  background-color: #f0f0f0;
  border: none;
  text-indent: 5px;
}

@media (max-width: 480px) {
  #container {
    display: flex;
    flex-direction: column;
    margin-top: unset;
  }
}
</style>