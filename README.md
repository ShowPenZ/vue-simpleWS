# vue-simple-ws

> A simple websocket component for Vue

## Installation

```
$ npm install vue-simple-ws --save
$ yarn add vue-simple-ws
```

## Usage

``` vue
<template>
  <div id="x">
    <VueSimpleWs
      url="ws://192.168.1.39:8081" //Replace with your ws URL
      @onOpen="onOpen"
      @onMessage="onMessage"
      @onError="onError"
      @onClose="onClose"
    />
    <button class="sendBtn" @click="onClick">发送</button>
    <p>{{wsData}}</p>
  </div>
</template>

<script type="text/babel">
import VueSimpleWs from "vue-simple-ws";

export default {
  name: "app",
  data() {
    return {
      wsData: "",
      sender: null
    };
  },
  components: {
    VueSimpleWs
  },
  methods: {
    onOpen(sender) {
      const that = this;

      that.sender = sender;
      sender("连接成功！");
    },
    onMessage(data) {
      const that = this;

      console.log(data);

      that.wsData = data;
    },
    onError(e) {
      console.log(e);
    },
    onClose(e) {
      console.log(e);
    },
    onClick() {
      const that = this;
      console.log(2342);

      that.sender("halo,it's me!");
    }
  }
};
</script>

<style>
#x {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.sendBtn {
  width: 80px;
  height: 40px;
  text-align: center;
  border: 1px solid red;
  border-radius: 8px;
  background-color: red;
  color: #fff;
  font-size: 24px;
  line-height: 24px;
}
</style>

```
# License

MIT
