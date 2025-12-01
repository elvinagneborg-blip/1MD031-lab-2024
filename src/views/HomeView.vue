<template>
<body>

<header class="header">
    <meta charset="utf-8"/>
    <head-img>    
    <img src="/img/burgerbanner.jpeg">
    </head-img>
    <h1> Välkommen till Burgerbutikens burgtastiska onlinesida </h1>
</header>

<main>
<h2> Välj burgarburgare </h2>
<h4>Det är här du väljer din burgarburgare!</h4>

  <section class="MenuItems">

    <Burger v-for="burger in burgers"
            v-bind:burger="burger" 
            v-bind:key="burger.name"
            v-on:update-order="updateBurgers"/>

  </section>

<h2>Kundinformation</h2>
Här lämnar du dina uppgifter för att beställa din burgarburgare! <div style="display: inline-block"><section class="obs">Fält märkta med * är obligatoriska.</section></div>
<h4>Leveransinformation</h4>

<section id="contact">
    <form>
        <p>
        <label for="kön">Kön *</label><br>
                <label for="man">Man</label>
                <input type="radio" id="man" v-model="order.gender" value="man"><br>

                <label for="kvinna">Kvinna</label>
                <input type="radio" id="kvinna" v-model="order.gender" value="kvinna"><br>

                <label for="annat">Annat</label>
                <input type="radio" id="annat" v-model="order.gender" value="annat"><br>
        </p>
        <p>
            <label for="förnamn">Förnamn *</label><br>
            <input type="text" id="förnamn" v-model="order.firstName" required="required" placeholder="förnamn">
        </p>
        <p>
            <label for="efternamn">Efternamn *</label><br>
            <input type="text" id="efternamn" v-model="order.lastName" required="required" placeholder="efternamn">
        </p>
        <p>
            <label for="email">E-post *</label><br>
            <input type="email" id="email" v-model="order.email" required="required" placeholder="epostadress">
        </p>
        <p>
            <label for="betalningsmetod">Betalningsmetod *</label><br>
            <select id="betalningsmetod" v-model="order.paymentMethod" required="required">
            <option selected="selected">Korvar</option>
            <option>Levande lamm</option>
            <option>Swosh</option>
            <option>Grus</option>
            <option>Komplimanger</option>
            </select>
        </p>
        <p>
            <label for="GDPR">Jag godkänner att mina uppgifter sparas enligt GDPR *</label><br>
            <input type="checkbox" id="GDPR" v-model="order.gdpr" required="required">
            </input>
        </p>
    
    <button type="submit" v-on:click.prevent="logOrder(); sendOrderToSocket();">
    <img src="/img/skicka.png" style="height: 25px;">
    Skicka beställning
    </button>

    </form>
</section>

<div id="map-container">
      <div id="map" v-on:click="setLocation">
        <div id="target" v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }">🚗</div>
      </div>
    </div>

</main>

<hr>
<footer>
    &copy; 2025 Burgarbutiken AB.
</footer>

</body>

</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, kCal, price, img, gluten, lactose , ingredients) {
  this.name = name;
  this.kCal = kCal;
  this.price = price;
  this.img = img;
  this.gluten = gluten;
  this.lactose = lactose;
  this.ingredients = ingredients;
};

const burgersArray = menu;

console.log(burgersArray);

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: burgersArray,
    order: {
    gender: 'annat',
    firstName: '',
    lastName: '',
    email: '',
    streetName: '',
    houseNumber: null,
    paymentMethod: 'Korvar',
    gdprAccepted: false
    },

    orderedBurgers: {},

    location: { x: 0, y: 0 }

  }
},

  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    logOrder: function () {
     console.log("Beställningsdata");
      console.log("Kön:", this.order.gender);
      console.log("Förnamn:", this.order.firstName);
      console.log("Efternamn:", this.order.lastName);
      console.log("E-post:", this.order.email);
      console.log("Gatunamn:", this.order.streetName);
      console.log("Husnummer:", this.order.houseNumber);
      console.log("Betalningsmetod:", this.order.paymentMethod);
      console.log("GDPR Godkänt:", this.order.gdpr);
      console.log("Leveranskoordinater:", this.location);
      console.log("Beställda Burgare"); 
      console.log("Burgare:", this.orderedBurgers);
      console.log("Hela Orderobjektet (formulär):", this.order);
    },

    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location.x = event.clientX - offset.x;
      this.location.y = event.clientY - offset.y;
    },
    
    sendOrderToSocket: function () {
      socket.emit("addOrder", { 
        orderId: this.getOrderNumber(),
        customerInfo: {
          gender: this.order.gender,
          firstName: this.order.firstName,
          lastName: this.order.lastName,
          email: this.order.email,
          paymentMethod: this.order.paymentMethod
        },

        orderItems: this.orderedBurgers, 
        details: { 
          x: this.location.x, 
          y: this.location.y  
        }

      });
    },

    updateBurgers: function(burgerUpdate) {
      this.orderedBurgers[burgerUpdate.name] = burgerUpdate.amount;
      if (burgerUpdate.amount === 0) {
        delete this.orderedBurgers[burgerUpdate.name];}
    } 
  }
}

</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');

  #map-container {
    width: 100%;
    max-height: 400px;
    overflow: auto;
  }

  #map {
    position: relative; 
    background-image: url("/img/polacks.jpg"); 
    background-size: cover;
    background-repeat: no-repeat;
    width: 1920px;
    height: 1078px;
  }

#target {
    position: absolute;
    font-size: 24pt;
    font-weight: bold;
    color: red;
    transform: translate(-50%, -50%); 
    pointer-events: none;
}

/*All text on the website*/
body {
    font-size: 14pt; 
    font-family: arial;
}

/*In order to get som "air" on the top of the page*/
header {
    margin-top: 80px;
}

/*Positioning the h1 inside the header image*/
header h1 {
    position: absolute;
    margin-top: -76px;
    margin-left: 65px;
}

.header {
    padding-right: 10px;
    overflow: hidden;
}

.header img {
    opacity: 0.6;
    width: 98%;
    height: 100px;
    border: solid black 2px;
}

section {
    margin-top: 20px;
}

.MenuItems {
    display: grid;
    grid-gap: 5px;
    grid-template-columns: 470px 470px 470px;
    background-color: black;
    color: white;
    padding: 10px;
    border: 2px dashed white;
}

#contact {
    padding: 10px;
    border: 2px dashed black;
}

#contact p {
    padding: 5px;
}

h1 {
    font-family: 'Courier New', monospace;
    font-size: 30pt;
    font-weight: bold;
    text-decoration: underline overline;
    color: rgb(181, 34, 34);
}

/*I just want some underlining to my h2 elements*/
h2 {
    text-decoration: underline;
}

.varning {
    font-weight: bold;
    color: red;
}

.obs {
    font-style: italic;
    font-weight: bold;
}

button, input[type="radio"]:hover {
    background-color: lightgray;
    cursor: pointer;
}

</style>