<template>
    <div class = burger a>
            <h3>{{ burger.name }}</h3>
            <img v-bind:src="burger.img" style="height: 150px;">
            <ul>
                <li>{{burger.kCal + " kCal."}}</li> 
                <li>{{burger.desc}} </li>
                <!-- Jag tyckte det blev snyggare och tydligare om både om och inte om är med-->
                <li v-if="burger.gluten"> Innehåller gluten.</li>
                <li v-if="burger.lactose"> Innehåller laktos.</li>
                <li v-if="!burger.gluten"> Innehåller inte gluten.</li>
                <li v-if="!burger.lactose"> Innehåller inte laktos.</li>
            </ul>
        <div>
          <button @click="decreaseAmount">-</button>
          <span> {{ "  " + amountOrdered + "  "}} </span>
          <button @click="increaseAmount">+</button>
        </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'OneBurger',
    props: {
      burger: Object
    },
    data: function () {
      return {
        amountOrdered: 0,
        }
    }, 
    methods: {
      increaseAmount() {
        this.amountOrdered++
        this.addBurger();
      }, 
      decreaseAmount(){
        if (this.amountOrdered > 0) {
          this.amountOrdered--;
          this.addBurger();
        }
      },
      addBurger: function () {
          this.$emit('orderedBurger', { 
            name:   this.burger.name, 
            amount: this.amountOrdered 
            
            }
          );
        },
    }
  }
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  .burger {
    color: #fff;
    border-radius: 5px;
    padding: 20px;
    font-size: 16px;
  }
  </style>
  