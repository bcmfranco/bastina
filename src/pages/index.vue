<template>
  <div class="magic_bn_arrow" id="container">
    <div @click="deletedAll()">
      <Brander />
    </div>

    <div id="content">
      <div id="input_wrapper">
        <input type="number" id="add_item" v-model.number="newItem" placeholder="Nuevo ítem" />
        <button @click="addItem(1)">fixed</button>
        <button @click="addItem(2)">viariable</button>
      </div>

      <div id="item_list">
        <div v-for="item in sortedItems" :key="item.id" class="item">
          <span>{{ item.value }} {{ item.type }}</span>
          <button @click="deleteItem(item.id)">x</button>
        </div>
      </div>

      <div class="total_wrapper" id="clean_btn" @click="deletedAll()">
        Limpiar
      </div>

      <div class="total_wrapper">
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
    deletedAll(){
      this.items = [];
      this.itemsVariable = [];

      return this;
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
      return `${this.total} / ${this.fixedPercentage}%`;
    },
    totalVariablePercentage() {
      this.variablePercentage = Math.round(100 / this.totalMax * this.totalVariable);
      return `${this.totalVariable} / ${this.variablePercentage}%`;
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
  font-family: "Montserrat", sans-serif;
  height: 100vh;
  overflow: hidden;
}

.magic_bg_snow{
  --s: 45px;
  --c1: #dedede;
  --c2: #4da5ed;
  --_g: radial-gradient(calc(var(--s)/2),var(--c1) 97%,#0000);
  background:
    var(--_g),var(--_g) calc(2*var(--s)) calc(2*var(--s)),
    repeating-conic-gradient(from 45deg,#0000 0 25%,var(--c2) 0 50%) calc(-.707*var(--s)) calc(-.707*var(--s)),
    repeating-linear-gradient(135deg,var(--c1) calc(var(--s)/-2) calc(var(--s)/2),var(--c2) 0 calc(2.328*var(--s)));
  background-size: calc(4*var(--s)) calc(4*var(--s));
}

.magic_bn_arrow{
  --s: 150px; /* control the size */
  --c:#4da5ed;
  
  --l:var(--c) 20%,#0000 0;
  --g:35%,#8fbee5 0 45%,var(--c) 0;
  background:
    linear-gradient(45deg,var(--l) 45%,var(--c) 0 70%,#0000 0),
    linear-gradient(-45deg,var(--l) var(--g) 70%,#0000 0),
    linear-gradient(45deg,var(--c) var(--g));
  background-size: var(--s) var(--s);
}

.magic_bn_floor{
  --s: 200px; /* control the size */
  --c: #fff; /* first color */
  
  --_g: #0000 8%,var(--c) 0 17%,#0000 0 58%;
  background: 
    linear-gradient(135deg,#0000 20.5%,var(--c) 0 29.5%,#0000 0) 0 calc(var(--s)/4),
    linear-gradient( 45deg,var(--_g)) calc(var(--s)/2) 0,
    linear-gradient(135deg,var(--_g),var(--c) 0 67%,#0000 0),        
    linear-gradient( 45deg,var(--_g),var(--c) 0 67%,#0000 0 83%,var(--c) 0 92%,#0000 0),
    #1095c1; /* second color */
  background-size: var(--s) var(--s);
}

#content {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

#input_wrapper {
  display: grid;
  grid-template-columns: 50% 25% 25%;
  align-items: center;
  gap: 5px;
  width: 300px;
  margin-bottom: 20px;
  padding: 10px 15px;
  border-radius: 10px;
  background-color: #1e4c7d;
}

#input_wrapper input{
  background-color: inherit;
  border: none;
  border-bottom: 1px dashed #e4e4e4;
  height: 34px;
  color: #e4e4e4;
}

#input_wrapper #add_item::placeholder{
  color: #e4e4e4;
}

#input_wrapper #add_item{
  width: 100px
}

button,
a {
  padding: 10px 20px;
  border: none;
  border-bottom: 1px solid #e4e4e4;
  background-color: inherit;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

#saving_wrapper{
  display: flex;
  flex-direction: column;
  width: 300px;
  margin: 10px 0px;
  border-radius: 10px;
  background-color: #ae2f2f;
  padding: 10px 15px;
}

#saving_wrapper #phone_number{
  height: 30px;
  border: none;
  border-bottom: 1px dashed #e4e4e4;
  background-color: inherit;
  text-align: center;
  font-size: 16px;
  color: #e4e4e4;
}

#saving_wrapper button{
  margin: 10px 0px;
}

button:hover {
  background-color: #0056b3;
}

#item_list {
  width: 300px;
  min-height: 300px;
  border-radius: 10px;
  padding: 10px 15px;
  background-color: #1e4c7d;
  color: #e4e4e4;
}

#item_list button{
  margin: 10px 0px;
  padding: 10px 20px;
  border-bottom: 1px solid #e4e4e4;
  border-radius: 0px;
  color: #e4e4e4;
  background-color: inherit;
  cursor: pointer;
  transition: background-color 0.3s;
}

.item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  height: 36px;
  padding: 3px 0px;
  text-indent: 10px;
}

.total_wrapper {
  display: flex;
  flex-direction: column;
  width: 300px;
  margin-top: 20px;
  padding: 10px 15px;
  border-radius: 10px;
  background-color: #ae2f2f;
  font-size: 16px;
  color: #e4e4e4;
}

.total_wrapper .total_input{
  width: 100px;
  font-size: 16px;
}

.total_wrapper #type_totals{
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.total_wrapper .type_totals_wrappers{
  display: grid;
  grid-template-columns: 50% 50%;
  line-height: 22px;
  text-indent: 5px;
  font-weight: unset;
  font-family: "Montserrat", sans-serif;
}

#clean_btn{
  text-align: center;
  color: #e4e4e4;
  cursor: pointer;
}

.type_totals_wrappers input{
  border: none;
  background-color: inherit;
  font-family: inherit;
  color: #e4e4e4;
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