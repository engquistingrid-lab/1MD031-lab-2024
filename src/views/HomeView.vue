<template>
  <header> 
        <h1 class="huvud">Välkommen till Smashy Burgers</h1>
        <img src='/img/header.jpeg' class="headerimage" style="width:100%"> 
    </header>
        <main> 
                <section id="meny"> 
                    <h2 class="menystart"> Välj burgare </h2>
                    <h3 class="menystart">Välj vilken burgare du vill beställa</h3>
                        <div class= "wrapper">
                          <Burger v-for ="burger in burgers"
                                  v-bind:burger = "burger"
                                  v-bind:key = "burger.name"
                                  v-on:burgerOrdered="addToOrder"/>   
                        </div>          
                </section>
                <section id="kundinfo">
                    <h2 class="information">Kundinformation </h2>
                    <h3 >Kön</h3>
                    <form>
                        <label><input type="radio" v-model="kön" value="Kvinna"> Kvinna</label>
                        <label><input type="radio" v-model="kön" value="Man"> Man</label>
                        <label><input type="radio" v-model="kön" value="Vill inte ange"> Vill inte ange</label>
                    </form>
                    <h3> Leveransinformation</h3>
                        <p>
                            <label for="Fullständigt namn">Fullständigt namn</label><br>
                            <input type="text" id="Fullständigt namn" v-model="fullständigtnamn" required="required" placeholder="För- och efternamn">
                        </p>
                        <p>
                            <label for="Email-adress">E-mail</label><br>
                            <input type="text" id="Email-adress" v-model="email" required="required" placeholder="Emailadress">
                        </p>
                        <p>
                            <label for="betalningsmetod">Betalningsmetod</label><br>
                            <select id="betalningsmetod" v-model="betalningsmetod">
                                <option selected="selected">Kontokort</option >
                                <option>Kreditkort</option>
                                <option>Swish</option>
                            </select>
                        </p>
                        <div class="upphämtning">
                            <div class="map" v-on:click="setLocation">
                                <h2>Välj upphämtningsplats för ordern</h2> 
                                <div id="dot"
                                    v-bind:style = "{left: location.x + 'px', 
                                                top: location.y + 'px'}">
                                                T
                                </div>
                            </div>
                            
                        </div>
                </section>
                <div class="knappwrapper">
                <button v-on:click = "addOrder" type="button" id="knapp">
                    Skicka min beställning
                    <img src="/img/skicka_knapp.jpeg">
                </button>
                </div>
        </main>
        <hr>
        <footer>
            End notes
        </footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json' 

let lastOrderNumber = 0;

const socket = io("localhost:3000");

function MenuItem (name, url, laktos, gluten, kCal,kött,svamp) {
    this.name = name;
    this.url = url;
    this.laktos = laktos;
    this.gluten = gluten;
    this.kCal = kCal;
    this.kött = kött;
    this.svamp = svamp;
    
  
}
let BurgerArray =[
  new MenuItem("THE CLASSIC","/img/theclassic.jpg",true,true,10,true,false),
  new MenuItem("THE SPICY","/img/thespicy.webp",true,true,10,true,false),
  new MenuItem("THE VEGGIE","/img/theveggie.jpg",true,true,10,false,true)
]


export default {
  name: 'HomeView',
  components: {
    Burger

  },
  data: function () {
    return{
        burgers : menu,
        kön: 'vill inte ange',
        fullständigtnamn:'',
        email:'',
        gata:'',
        hus:'',
        betalningsmetod:'Kontokort',
        orderedBurgers:{},
        location:{
            x:0,y:0
        }
    }
    
  },

  methods:{
    getOrderNumber: function () {
        lastOrderNumber +=1
      return lastOrderNumber;
    },
    addOrder: function(){

        console.log('Beställning skickad!')
        console.log('namn:', this.fullständigtnamn)
        console.log('Beställda burgare:',this.orderedBurgers)
        socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y},
                                orderItems: this.orderedBurgers,
                                customerInfo: {
                                    kundnamn: this.fullständigtnamn,
                                    mail: this.email,
                                    valdbetalning: this.betalningsmetod,
                                    könstillhörighet: this.kön
                                }
                              }
                 );


    },
    setLocation: function(event){
        this.location.x = event.offsetX;
        this.location.y= event.offsetY;
},

    addToOrder: function (event){
        if(event.amount> 0){
            this.orderedBurgers[event.name] = event.amount;
        }else{
            delete this.orderedBurgers[event.name]
        }
        
  },
    addBurger: function(){
        this.amountOrdered += 1;
        this.$emit('orderedBurger',{name:this.burger.name, amount:this.amountOrdered
        });
    }
    
  }
  
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
@import 'https://fonts.googleapis.com/css?family=Pacifico|Dosis';
body {
    font-family: "Times New Roman", sans-serif;
    font-size: 16pt; /* Your comment goes here */
}

p {
    color:black;
}

h1 {
    font-family: "Times New Roman";
    font-size: 36pt;
}

main {
    background-color: rgb(250, 249, 228);
}

.huvud{
    text-align: center;
    overflow: hidden;
}

header {
    width: 100%;
    margin: 20px,20px;
    overflow: hidden;
    height: 250px;
    padding: 50px,50px;
    position: relative;

}
.headerimage{
 width: 100%;
 height:auto;
 opacity: 0.2;
 padding: 50px,50px;
}
header h1{
    margin:0;
    padding: 0;
    position: absolute;
    top:50%;
    left:50%;
    transform: translate(-50%, -50%); 
    z-index: 2;

}
.knappwrapper{
    margin:30px;
    padding: 20px;
}


.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: bold;
}
.allergen {
   
   font-weight: bold;
}

.menystart{
    margin:30px;
}
.upphämtning{
    overflow: scroll;
 }


#meny {
   margin:30px;
    background-color:black ;
   color:rgb(250, 249, 228);
   border-style: dashed;
    border-color: rgb(250, 249, 228);
}
button:hover {
    cursor:pointer;
    color: #ffffff
    
}
label, 
input[type="radio"],
select
{
    cursor:pointer;
}
#knapp{
    margin: 30px, 530px;
    padding:30px, 30px;
}
#knapp img{
    width:30px;
    height: 30px;
    margin-left:10px;
    vertical-align: middle;
}

#kundinfo{
    border-style: dashed;
    border-color: black;
    margin:30px;
    padding:30px;
}
.kundinfotext{
    margin:20px, 20px;
    padding:30px;
}
.wrapper{
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    grid-gap: 10px;
}
.box{
    border-radius: 5px;
    padding: 20px;
    font-size: 150%;
}
.a {
     grid-column: 1 ;
 }
 .b{
    grid-column: 2;
 }
 .c{
    grid-column: 3;
 }
 .map{
    width:1920px;
    height:1078px;
    background: url("/img/polacks.jpg");
    position: relative;
    cursor: crosshair;
 }
 
 #dot{
     position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
    pointer-events: none;
 }
 @media screen and (max-width: 800px) {
    h1 {
        font-size: 6vw;
    }
}
</style>