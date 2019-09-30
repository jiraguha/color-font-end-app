<template>
    <div class="demo" id="list-complete-demo">

        <div class="flex-container ">
            <div>

                <h1>Color-service</h1>
                <input v-model="latency" placeholder="0"/>
                <button v-on:click="httpRequest">HTTP Request</button>
                <button v-on:click="clean">Clean</button>
                <transition-group name="list-complete" tag="p">
                    <div
                            class="list-complete-item"
                            v-bind:key="item"
                            v-for="item in items"
                    >
                        <div v-bind:style="{ backgroundColor: getColor(item.hexCode) }">
                            _
                        </div>
                    </div>
                </transition-group>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from "axios";
    import Buefy from 'buefy';
    import 'buefy/dist/buefy.css';

    export default {
        use: Buefy,
        name: "ColoredWithHttp",
        props: {},
        data: function () {
            return {
                latency: 0,
                items: [],
                nextNum: 1
            };
        },
        methods: {
            randomIndex: function () {
                return 0;
            },
            httpRequest: function () {
                // eslint-disable-next-line no-console
                console.log("Calling colors");
                axios({
                    method: "GET",
                    url: "http://localhost:8080/colors",
                    params: {latency: this.latency}
                }).then(
                    result => {
                        this.items.push(...result.data);
                        // eslint-disable-next-line no-console
                        console.log(result.data);
                    },
                    error => {
                        // eslint-disable-next-line no-console
                        console.error(error);
                    }
                );
            },
            add: function () {
                this.items.splice(this.randomIndex(), 0, this.nextNum++);
                if (this.items.length > 150) {
                    this.items.splice(this.items.length - 1, 1);
                }
            },
            remove: function () {
                this.items.splice(this.items.length - 1, 1);
            },
            clean: function () {
                this.items = [];
            },
            getColor(hexCode) {
                return hexToRgba(hexCode, 0.8);
            }
        }
    };

    function componentToHex(c) {
        var hex = c.toString(16);
        return hex.length == 1 ? "0" + hex : hex;
    }

    // eslint-disable-next-line no-unused-vars
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
        background-color: #2c3e50;
        height: 600px;
        width: 600px;
    }

    .flex-container > div {
        background-color: #f1f1f1;
        width: 100%;
        /*height: 100%;*/
        margin: 10px;
        padding: 20px;
    }

    .list-complete-item {
        transition: all 1s;
        display: inline-block;
        justify-content: space-around;
        align-items: center;
        width: 5%;
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

    h3 {
        margin: 40px 0 0;
    }
    ul {
        list-style-type: none;
        padding: 0;
    }
    li {
        display: inline-block;
        margin: 0 10px;
    }
    a {
        color: #42b983;
    }
</style>
