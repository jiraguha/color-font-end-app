<template>
    <div class="demo" id="list-complete-demo">
        <div class="flex-container ">
            <div>
                <h1>Color-service</h1>
                <input v-model="requestn" placeholder="150" type="number"/>
                <input v-model="latency" placeholder="0" type="number"/>
                <button v-on:click="requestStream">Resquest Stream</button>
                <button v-on:click="clean">Clean</button>
                <transition-group name="list-complete" tag="p">
                    <div
                            class="list-complete-item"
                            v-for="(hexCode, index) in codes"
                            v-bind:key="index + 10"
                    >
                        <div v-bind:style="{ backgroundColor: getColor(hexCode) }">
                            _
                        </div>
                    </div>
                </transition-group>
            </div>
        </div>
    </div>
</template>

<script>
    import Buefy from "buefy";
    import "buefy/dist/buefy.css";
    import {IdentitySerializer, JsonSerializer, RSocketClient} from "rsocket-core";
    import RSocketWebSocketClient from "rsocket-websocket-client/build/RSocketWebSocketClient";

    export default {
        use: Buefy,
        name: "ColorWithRsocket",
        props: {},
        data: function () {
            return {
                requestn: 150,
                wsClient: {},
                socket: {},
                latency: 0,
                items: [],
                codes: [],
                nextNum: 1
            };
        },
        mounted: function () {
            let wsClient = new RSocketClient({
                serializers: {
                    data: JsonSerializer,
                    metadata: IdentitySerializer
                },
                setup: {
                    // ms btw sending keepalive to server
                    keepAlive: 60000,
                    // ms timeout if no keepalive response
                    lifetime: 180000,
                    // format of `data`
                    dataMimeType: "application/json",
                    // format of `metadata`
                    metadataMimeType: "message/x.rsocket.routing.v0"
                },
                transport: new RSocketWebSocketClient({
                    url: "ws://localhost:8081"
                })
            });

            wsClient.connect().subscribe({
                onSubscribe: () => {
                    // eslint-disable-next-line no-console
                    console.log("Opening the ws connection");
                },
                onComplete: socket => {
                    this.socket = socket;
                    // socket provides the rsocket interactions fire/forget, request/response,
                    // request/stream, etc as well as methods to close the socket.
                    // c.close();
                },

                onError: error => {
                    // eslint-disable-next-line no-console
                    console.log(error);
                    // eslint-disable-next-line no-undef
                    // addErrorMessage("Connection has been refused due to ", error);
                }

                // onSubscribe: cancel => {
                //   /* call cancel() to abort */
                // }
            });
        },
        methods: {
            randomIndex: function () {
                return 0;
            },
            requestStream: function () {
                // eslint-disable-next-line no-console
                console.log("Opening the tpc connection");
                this.socket.requestStream({data: {latency: this.latency}}).subscribe({
                    // eslint-disable-next-line no-console
                    onComplete: () => {
                        // eslint-disable-next-line no-console
                        console.log("complete");
                    },
                    onError: error => {
                        // eslint-disable-next-line no-console
                        console.log(error);
                        // addErrorMessage("Connection has been closed due to ", error);
                    },
                    onNext: payload => {
                        // eslint-disable-next-line no-console
                        console.log(payload.data);
                        this.items.push(payload.data);
                        this.codes.push(payload.data.hexCode);

                    },

                    onSubscribe: subscription => {
                        subscription.request(parseInt(this.requestn));
                    }
                });
            },
            add: function () {
                this.items.splice(this.randomIndex(), 0, this.nextNum++);
                if (this.items.length > 150) {
                    this.items.splice(this.items.length - 1, 1);
                }
            },
            clean: function () {
                this.items = [];
                this.codes = [];
                // this.socket.close();
                // this.socket.open();
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
