<script>
import HelloWorld from "./components/HelloWorld.vue";
import ColoredCube from "./components/ColoredCube";
import NotFound from "./components/NotFound";
import ColorWithHttp from './components/ColorWithHttp';
import ColorWithRsocket from './components/ColorWithRsocket';

const routes = {
  "/": HelloWorld,
  "/colored-cube": ColoredCube,
  "/color-http": ColorWithHttp,
  "/color-ws": ColorWithRsocket
};
export default {
  name: "app",
  data() {
    return {
      currentRoute: window.location.pathname
    };
  },
  computed: {
    ViewComponent() {
      return routes[this.currentRoute] || NotFound;
    }
  },
  render(h) {
    return h(this.ViewComponent);
  }
};
</script>

<style>

</style>
