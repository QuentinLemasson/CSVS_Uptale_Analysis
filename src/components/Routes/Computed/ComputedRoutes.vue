<template>
  <div />
  <Legend @check="legendSelector" :categories="mapToArray(categories)" />
  <Checkbox @check="mergeThematic" :name="'Merge thematics'" />
  <Paths :paths="computedPaths" :display="'compute'" :categories="categories" :start='paginationStart' :end='paginationEnd'/>
  <PaginationBar @pageChange="pageChange" :min='0' :max='computedPaths.length' :start='paginationStart' :range='paginationRange'/>
</template>

<script>
import { mapToArray } from "../../../utilities";

import Legend from "./Legend/Legend.vue";
import Checkbox from "./Checkbox.vue";
import Paths from "../Paths.vue";
import PaginationBar from "../PaginationBar.vue";

export default {
  name: "ComputedRoutes",
  emits: ["updateAndComputePaths", "mergeThematic"],
  components: {
    Legend,
    Checkbox,
    Paths,
    PaginationBar,
  },
  props: {
    categories: {
      type: Map,
      default: [],
    },
    computedPaths: {
      type: Array,
      default: [],
    },
    paginationRange : {
      type: Number,
      default: 10
    }
  },
  data(){
    return{
      paginationStart : 0,
      paginationEnd : this.paginationRange>this.computedPaths.length?this.computedPaths.length:this.paginationRange
    }
  },
  methods: {
    legendSelector(e) {
      this.categories.get(e.id).whitelisted = e.checked;
      this.$emit("updateAndComputePaths");
    },
    mapToArray(data) {
      return mapToArray(data);
    },
    mergeThematic(e) {
      this.$emit("mergeThematic", e);
    },
    pageChange(start){
      this.paginationStart = start;
      this.paginationEnd = start+this.paginationRange>this.computedPaths.length?this.computedPaths.length:start+this.paginationRange
    }
  },
};
</script>

<style scoped>
</style>