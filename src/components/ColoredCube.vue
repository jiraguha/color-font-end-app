<template>
  <div class="demo" id="list-complete-demo">
    <button v-on:click="add">Add</button>
    <button v-on:click="remove">Remove</button>
    <div class="flex-container ">
      <div>
        Color-service
        <transition-group name="list-complete" tag="p">
          <span
            class="list-complete-item"
            v-bind:key="item"
            v-bind:style="{ backgroundColor: getColor() }"
            v-for="item in items"
          >
              _
          </span>
        </transition-group>
      </div>
      <div>
        Number-service
        <transition-group name="list-complete" tag="p">
          <span
            class="list-complete-item"
            v-bind:key="item"
            v-for="item in items"
          >
            {{ item }}
          </span>
        </transition-group>
      </div>
      <div>
        Front
        <transition-group name="list-complete" tag="p">
          <span
            class="list-complete-item"
            v-bind:key="item"
            v-bind:style="{ backgroundColor: getColor() }"
            v-for="item in items"
          >
            {{ item }}
          </span>
        </transition-group>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ColoredCube",
  data: function() {
    return {
      items: [],
      nextNum: 1
    };
  },
  methods: {
    randomIndex: function() {
      return 0;
    },
    add: function() {
      this.items.splice(this.randomIndex(), 0, this.nextNum++);
      if (this.items.length === 50) {
        this.items.splice(this.items.length - 1, 1);
      }
    },
    remove: function() {
      this.items.splice(this.items.length - 1, 1);
    },
    getColor() {
      return hexToRgba("#007080", 0.8);
    }
  }
};

function componentToHex(c) {
  var hex = c.toString(16);
  return hex.length == 1 ? "0" + hex : hex;
}

function rgbToHex(r, g, b) {
  return "#" + componentToHex(r) + componentToHex(g) + componentToHex(b);
}

function hexToRgba(hex, alpha) {
  var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
  var finalAlpha = alpha && alpha <= 1 && alpha >= 0 ? alpha : 1;
  if (result) {
    var r = parseInt(result[1], 16);
    var g = parseInt(result[2], 16);
    var b = parseInt(result[3], 16);
    return `RGBA(${r},${g},${b},${finalAlpha})`;
  }
  return null;
}
</script>

<style scoped>
.flex-container {
  display: flex;
  background-color: grey;
  height: 300px;
}

.flex-container > div {
  background-color: #f1f1f1;
  width: 30%;
  height: 100%;
  margin: 10px;
  padding: 20px;
}

.list-complete-item {
  transition: all 1s;
  display: inline-block;
  justify-content: space-around;
  align-items: center;
  width: 10%;
  height: 10%;
  border: 1px solid #aaa;
  margin-right: -1px;
  margin-bottom: -1px;
}

.list-complete-enter, .list-complete-leave-to
        /* .list-complete-leave-active below version 2.1.8 */
 {
  opacity: 0;
  transform: translateY(30px);
}

.list-complete-leave-active {
  position: absolute;
}

.cell:nth-child(3n) {
  margin-right: 0;
}
</style>
