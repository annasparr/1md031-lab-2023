<template>
  <header id = "header">
      <h1>Välkommen till BurgerGate!</h1>
  </header>
  <br>
  <section id="burgers">
    <h2>Välj din kontroversiella burgare!</h2>
        <p>Det här är var du väljer din burgare.</p>
        <div class = burgers>
            <div class="burgers">
              <Burger v-for="burger in menu" 
              v-bind:burger="burger" 
              v-bind:key="burger.name"
              v-on:orderedBurger="addToOrder($event)" />
            </div>
        </div>
</section>
<br>
<section id="info">
    <h2>Leveransinstruktioner</h2>
    <p>Det här är var du fyller i den informationen vi behöver för att kunna leverera din burgare.</p>
    <br>
    <p>
        <label for="name">Namn</label><br>
        <input type="text" id="name" v-model="name" required="required" placeholder="Förnamn+Efternamn">
    </p>
    <p>
        <label for="mail">E-mail</label><br>
        <input type="email" id="email" v-model="email" required="required" placeholder="E-mail address">
    </p>
    <!--
    <p>
        <label for="Gata">Gata</label><br>
        <input type="text" id="street" v-model="street" placeholder="Gatunamn">
    </p>
    <p>
        <label for="Gatunummer">Gatunummer</label><br>
        <input type="number" id="streetnumber" v-model="sn" placeholder="Gatunummer">
    </p>
    -->
    <div class="mapcontainer">
      <div id="map" v-on:click="setLocation">
        Klicka för att lägga in din position.
        <div id="dots">
          <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }">
            T
          </div>
        </div>
      </div>
    </div>
    <p>
        <label for="Betalningsmetod">Betalningsmetod: </label><br>
        <select id="payment" v-model="payment">
            <option>Swish</option>
            <option>Krita</option>
            <option selected="selected">Betalkort</option>
            <option>Gratis!</option>
        </select>
     </p>

     <p>
      <label for="Kön">Ange ditt kön:</label><br>
      <input type="radio" id="kvinna" v-model="gender" value="Kvinna" checked>
      <label for="kvinna">Kvinna</label><br>
      <input type="radio" id="man" v-model="gender" value="Man">
      <label for="man">Man</label><br>
      <input type="radio" id="oklart" v-model="gender" value="Annat/vill inte ange">
      <label for="oklart">Annat/vill inte ange</label><br>
    </p>
    <button type="button" v-on:click= "submitForm()">
        <img src=https://static.vecteezy.com/system/resources/previews/021/952/564/original/free-tasty-hamburger-on-transparent-background-free-png.png style="width: 40px;">
        Skicka Info!
    </button>
</section>
<br>
<section id="receipt" style="display: none;">
    <h3>Kvitto</h3>
    <p>Tack för din beställning, {{ name }}! Här är ditt kvitto:</p>
      <p>Namn: {{ name }} </p>
      <p>Mail: {{ email }} </p>
      <p>Kön: {{ gender }}</p>
      <p v-for="(item, itemKey) in orderedBurgers" :key="itemKey">
            {{ 'Burgare: ' + itemKey }}, antal: {{ item }}</p>
      <p>Betalmetod: {{ payment }}</p>
</section>


    <hr>
     <footer>
      <p> &copy; 2023 Skandalburgare</p>
     </footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import menu from '../assets/menu.json'
import io from 'socket.io-client'

const socket = io();

//function MenuItem(name, img, kCal, gluten, lactose, desc) {
   // this.name = name; // The *this* keyword refers to the object itself
    // this.img = img;
    // this.kCal = kCal;
    // this.gluten = gluten;
    // this.lactose = lactose;
    // this.desc = desc;
    // };


//const menuItems = [
  //new MenuItem("CheeseGate", "https://img.koket.se/standard-large/tommy-myllymakis-saftiga-cheeseburgare.jpg", 600, true, true, "Inspirerad av skandalernas land, USA!"),
  //new MenuItem("WaterGate", "https://shared.cdn.smp.schibsted.com/v2/images/82f3fa85-7b4b-4ccc-a7c7-213800dc2a15?fit=crop&h=750&w=1000&s=845630ab51d9b4e28369dc892af7c4a7b19baed8", 500, true, false, "Vår, liksom WaterGate, äldsta och mest hemliga burgare!"),
  //new MenuItem("VeganGate", "https://www.thespruceeats.com/thmb/S3bw6OQYOHY2lfEHhdHn1U6wW_M=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/vegan-mushroom-bean-burger-recipe-3378623-13_preview1-5b241897fa6bcc0036d2c9bf.jpeg", 400, false, false, "Ett alternativ för alla veganer där ute!")
  //];


export default {
  name: 'HomeView',
  components: {
    Burger
  },
  
  data: function () {
    return {
      menu: [],
      name: '', // Individual data properties for form values
      email: '',
      //street: '',
      //sn: '',
      payment: 'Betalkort',
      gender: 'Kvinna',
      orderedBurgers: {},
      location: { x: 0, y: 0 } 
    }
  },
  mounted() {
    this.menu = menu; // Assign the fetched menu data to the component's menu property
  },
  methods: {
    submitForm: function() {
      alert("Du har lagt din order, king!")
      console.log('Submitted order: ');
      console.log('Name: ' + this.name);
      console.log('Email: ' + this.email);
      //console.log('Address:' + this.street + this.sn);
      console.log('Ordered Burgers:'+ JSON.stringify(this.orderedBurgers));
      console.log('Payment Option:'+ this.payment);
      console.log('Gender:'+ this.gender);
      socket.emit("addOrder", {orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                            y: this.location.y + 11 },
                                orderItems: this.orderedBurgers,
                                customerInfo: {Namn: this.name, 
                                              Email: this.email,
                                              Betalmetod: this.payment,
                                              Kön: this.gender}
                              }                   
                );
      document.getElementById('receipt').style.display = 'block';
      },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
      console.log('Ordered Burgers:', this.orderedBurgers);
    },
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location.x = event.clientX -11 - offset.x;
      this.location.y = event.clientY -27 - offset.y;
    },
    setLocation: function (event) {
      this.location.x = event.clientX -11 - event.currentTarget.getBoundingClientRect().left;
      this.location.y = event.clientY -27 - event.currentTarget.getBoundingClientRect().top;
      }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Lato');

#map {
  background: url('../../public/img/polacks.jpg');
  width: 1920px;
  height: 1078px;
  }

.mapcontainer {
  width: 100%;
  height: 400px;
  overflow: scroll;
  }

header {
    align-items: center; /* Center items vertically */
    justify-content: center; /* Center items horizontally */
    text-align: center; /* Center text inside the header */
    color: #ffffff; /* Set text color to white */

    background-image: url('https://blog.abim.org/wp-content/uploads/2020/01/restaurant-scaled.jpeg');
    background-size: cover; /* Cover the entire viewport */
    background-position: center; /* Center the image */
    height: 70vh; /* Set the height to 100% of the viewport height */
  }

header h1 {
    margin: 0;
    position: relative; /* Add relative positioning */
    line-height: 15; /* Adjust the line-height */
    text-shadow: 2px 2px 4px #000000;
  } 

section {
    margin: 20px, 10px, 10px, 10px; /* Add top margin */
  }

#receipt {
  display: inline-block;
  max-width: fit-content;
  border: 2px dotted #3c7735; /* Black dashed border */
  padding: 7px; /* Adjust padding for space inside the border */
  border-radius: 15px;
  }


p {
    margin-top: 10px; /* Add top margin */
    margin-bottom: 10px; /* Add bottom margin */
  }


#burgers div {
    margin: 2px; /* Add margin around the div elements */
    padding: 2px; /* Add padding inside the div elements */
  }

#burgers h2, #burgers h3, #info h2, #info button, #burgers p, #info p, #burgers img {
    margin-left: 10px; /* Add left margin */
  } 

.burgers {
    display: grid;
    grid-gap: 2px;
    grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
    background-color: #000;
  }

.burger {
    color: #ffffff;
    border-radius: 5px;
    padding: 20px;
    font-size: 16px;
  }

#info button {
    margin-bottom: 10px;
  }

body {
    font-family: 'Lato', "Times New Roman", "Courier New", sans-serif;
  }

  #burgers {
      border: 2px dashed #98e4a8;; 
      background-color: #000000; /* Black background color */
      color: #ffffff; /* White text color */
      border-radius: 25px;  
  }
  
  #info {
      border: 2px dashed #3c7735; /* Black dashed border */
      background-color: #98e4a8; /* White background color */
      border-radius: 25px;
  }

  button:hover {
    background-color: #20792f;
    cursor: pointer;
  }
</style>