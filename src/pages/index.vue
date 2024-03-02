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
          <button @click="deleteItem(item.id)">x</button>
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
        <input type="text" class="" id="phone_number" v-model="phoneNumber" placeholder="+543413690080"/>
        <button @click="prepareWhatsAppMessage">Enviar datos por WhatsApp</button>
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
      fixedPercentage: 50,
      wsp_content: "Vacío",
      sortedItems: [],
      phoneNumber: "+543413690080"
    };
  },
  mounted() {

    // Read token and write items
    var token = window.location.search.substring(1);
    if(token){
      var parts = token.split("zzz");
      var allItems = parts.map(part => {
        var [id, value, type] = part.split(",");
        return { id: parseInt(id), value: parseInt(value), type: type === "f" ? "fixed" : "variable" };
      });

      allItems.forEach(item => {
        if(item.type === "fixed") {
          this.items.push(item);
        } else {
          this.itemsVariable.push(item);
        }
      });

      this.fixedSum();
      this.variableSum();
      this.maxSum();

    }
    ////////////////
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
    prepareWhatsAppMessage() {
      // Genero token
      if(this.items[0] || this.itemsVariable[0]){
        var allItems = [...this.items, ...this.itemsVariable].sort((a, b) => a.id - b.id);
        
        var itemJoined = allItems.map(item => {
          return item.id + "," + item.value + "," + (item.type === "fixed" ? "f" : "v");
        }).join("zzz");
      }

      // Obtener el número de teléfono del input
      this.phone_number = this.phoneNumber;

      // Paso el token como contenido de wsp
      this.wsp_content = itemJoined;
      window.location.href = 'https://api.whatsapp.com/send?phone=' + this.phone_number +'&text=' + window.location.href + "?" + encodeURIComponent(this.wsp_content);
    }
  },
  computed: {
    sortedItems() {
      var allItems = [...this.items, ...this.itemsVariable];
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
  /* background: rgb(246,151,218);
  background: linear-gradient(312deg, rgba(246,151,218,1) 18%, rgba(244,237,135,1) 62%); */
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
  flex-direction: row;
  align-items: center;
  margin-bottom: 20px;
}

button,
a {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: rgba(0, 0, 0, 0.1);
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

#saving_wrapper{
  margin: 10px 0px;
  padding: 10px 10px;
  display: flex;
  flex-direction: column;
  width: 266px;
}

#saving_wrapper button{
  margin: 10px 0px;
}

#saving_wrapper a{
  text-decoration: none;
}

button:hover {
  background-color: #0056b3;
}

#item_list {
  width: 300px;
  padding: 10px 15px;
  background-color: #1e4c7d;
  color: #e4e4e4;
}

#item_list button{
  padding: 10px 20px;
  border-bottom: 1px solid #e4e4e4;
  border-radius: 0px;
  color: #e4e4e4;
  cursor: pointer;
  background-color: inherit;
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
  padding: 3px 0px;
}

#total_wrapper {
  display: flex;
  flex-direction: column;
  margin-top: 20px;
  width: 300px;
  background-color: #ae2f2f;
  color: #e4e4e4;
  padding: 10px 15px;
  font-size: 16px;
}

#total_wrapper .total_input{
  width: 100px;
  font-size: 16px;
}

#total_wrapper #type_totals{
  display: flex;
  flex-direction: column;
  gap: 5px;
}

#total_wrapper .type_totals_wrappers{
  display: grid;
  grid-template-columns: 50% 50%;
  font-weight: unset;
  font-family: "Montserrat", sans-serif;
  text-indent: 5px;
  line-height: 22px;
}

.type_totals_wrappers input{
  font-family: inherit;
  color: #e4e4e4;
  background-color: inherit;
  border: none;
}

.type_totals_wrappers #max_total{
  width: 266px;
}



@media (max-width: 480px) {
  #container {
    display: flex;
    flex-direction: column;
    margin-top: unset;
  }
}
</style>