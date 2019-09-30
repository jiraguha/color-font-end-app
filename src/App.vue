<script>
import HelloWorld from "./components/HelloWorld.vue";
import ColoredCube from "./components/ColoredCube";
import NotFound from "./components/NotFound";
import Colored from "./components/Colored";

const routes = {
  "/": HelloWorld,
  "/colored-cube": ColoredCube,
  "/colored": Colored
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

<style></style>
