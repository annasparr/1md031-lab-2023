<template>
  <div id="orders">
    <div id="orderList">
      
      <div v-for="(order, key) in orders" v-bind:key="'order'+key">
        {{'Ordernummer: ' +  key }}
        <ul>
          <p v-for="(item, itemKey) in order.orderItems" :key="itemKey">
            {{ 'Burgare: ' + itemKey }}, antal: {{ item }}
          </p>
          </ul>

          <ul>
          <p v-for="(item,itemKey) in order.customerInfo" :key="itemKey">
            {{ itemKey }}: {{ item }}
          </p>
          </ul>
          <hr>
      </div>
      <button v-on:click="clearQueue">Clear Queue</button>
    </div>
    <div id="dots" v-bind:style="{ background: 'url(' + require('../../public/img/polacks.jpg')+ ')' }">
        <div v-for="(order, key) in orders" v-bind:style="{ left: order.details.x + 'px', top: order.details.y + 'px'}" v-bind:key="'dots' + key">
          {{ key }}
        </div>
    </div>
  </div>
</template>
<script>
import io from 'socket.io-client'
const socket = io();

export default {
  name: 'DispatcherView',
  data: function () {
    return {
      orders: null
    }
  },
  created: function () {
    socket.on('currentQueue', data =>
      this.orders = data.orders);
  },
  methods: {
    clearQueue: function () {
      socket.emit('clearQueue');
    }
  }
}
</script>
<style>
  @import url('https://fonts.googleapis.com/css?family=Lato');

body {
    font-family: 'Lato', "Times New Roman", "Courier New", sans-serif;
}

#orderList {
  top:1em;
  left:1em;
  position: absolute;
  z-index: 2;
  color:rgb(255, 123, 123);
  text-shadow: 2px 2px 4px #ffffff;
  background: rgba(255, 204, 204, 0.5);
  padding: 1em;
}
#dots {
  position: relative;
  margin: 0;
  padding: 0;
  background-repeat: no-repeat;
  width:1920px;
  height: 1078px;
  cursor: crosshair;
}

#dots div {
  position: absolute;
  background: rgb(108, 0, 0);
  color: rgb(245, 112, 112);
  border-radius: 10px;
  width:20px;
  height:20px;
  text-align: center;
}
</style>
