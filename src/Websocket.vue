/*eslint function-call-argument-newline: ["error", "never"]*/
<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
// const typeOf = type => object =>
//   Object.prototype.toString.call(object) === `[object ${type}]`;
// const isFunction = typeOf("Function");

export default {
  name: 'Vue-simple-Ws',
  props: {
    url: {
      type: String,
      required: true,
    },
    debug: {
      type: Boolean,
      default: false,
    },
    reconnect: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    const that = this;

    return {
      instance: null,
      reconnectFailureTimes: 0,
      reconnectTimer: null,
      needReconnet: true,
      WS: window.WebSocket ? new window.WebSocket(that.url) : new window.MozWebSocket(that.url),
    };
  },
  watch: {},

  mounted() {
    const that = this;

    that.structureWebSocket();
  },
  beforeDestroy() {
    const that = this;

    that.destroy();
  },
  methods: {
    structureWebSocket() {
      const that = this;

      that.WS.onopen = () => {
        that.printLog('ws is connected');

        that.$emit('onOpen', that.sendData);
      };

      that.WS.onmessage = data => {
        that.printLog(`wsData:${data.data}`);

        that.$emit('onMessage', data.data);
      };

      that.WS.onclose = e => {
        const { code, reason, wasClean } = e;

        that.printLog(`WS is disconneted,reason:${reason}`);

        that.$emit('onClose', code, reason, wasClean);

        if (that.reconnect && that.needReconnet) {
          that.reconnectFailureTimes++;

          if (that.reconnectFailureTimes < 3) {
            that.reconnectTimer = window.setTimeout(() => {
              that.setState({
                WS: window.WebSocket ? new window.WebSocket(that.url) : new window.MozWebSocket(that.url),
              });
              that.structureWebSocket();
            }, 15 * 1000);
          }
        }
      };

      that.WS.onerror = e => {
        that.$emit('onError', e);
      };
    },
    sender(msg) {
      const that = this;

      if (that.WS && that.WS.readyState === 1) {
        that.WS.send(msg);
      }
    },

    sendData(msg) {
      const that = this;

      return that.sender(msg);
    },

    printLog(val) {
      const that = this;

      if (that.debug) {
        console.log(val);
      }
    },
    destroy() {
      const that = this;

      that.needReconnet = false;

      clearTimeout(that.reconnectTimer);

      that.printLog(`WS is close`);

      that.WS.close();
    },
  },
};
</script>
<style scoped></style>
