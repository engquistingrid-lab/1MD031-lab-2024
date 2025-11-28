<template>
  <div class= "box">
   
    <h3> {{ burger.name }}</h3>
        <img v-bind:src="burger.url" style="width:150px; height:auto;">
            <ul>
            <li v-if = "burger.laktos">laktos</li>
            <li v-if = "burger.gluten">gluten</li>
            <li v-if = "burger.kött">svenskt nötkött</li>
            <li v-if = "burger.svamp">portabellosvamp</li>
            <li> kCal: {{burger.kCal}} </li>
            </ul>
            <h5>Antal i varukorgen: {{ amountOrdered }}</h5>
            
            <div class="amountbutton">
              Klicka för att lägga i varukorgen:
              <button v-on:click="changeOrder(1)">+</button>
              <button v-on:click="changeOrder(-1)">-</button>
            </div>
            
</div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data:function(){
    return{
      amountOrdered:0
    }
  },
  methods:{
    changeOrder: function(amount){
      if (this.amountOrdered === 0 && amount<0){
        return;
      }
      this.amountOrdered += amount;
      this.$emit('burgerOrdered',{
        name: this.burger.name,
        amount: this.amountOrdered
      });
    }
  
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.allergen {
   font-weight: bold;
}
.amountbutton{
  display:flex;
  justify-content: left;
  font-size: 0.8em;
  color:rgb(250, 249, 228) ;
}
.amountbutton button{
  padding: 20px 20px;
  margin: 0px 5px;
  font-size: 1.5em;
  color:rgb(250, 249, 228);
  border-radius: 10px;
  border-color:rgb(250, 249, 228) ;
  background-color: black;
}
</style>