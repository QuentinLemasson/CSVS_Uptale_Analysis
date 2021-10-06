<template>
  <Paths :paths="paths" :display="'all'" :start='paginationStart' :end='paginationEnd'/>
  <PaginationBar @pageChange="pageChange" :min='0' :max='paths.length' :start='paginationStart' :range='paginationRange'/>
</template>

<script>
import Paths from "../Paths.vue";
import PaginationBar from "../PaginationBar.vue";

export default {
  name: "AllRoutes",
  props: {
    paths: {
      type: Array,
      default: [],
    },
    paginationRange : {
      type: Number,
      default: 10
    }
  },
  data(){
    return {
      paginationStart : 0,
      paginationEnd : this.paginationRange>this.paths.length?this.paths.length:this.paginationRange
    }
  },
  components: {
    Paths,
    PaginationBar,
  },
  methods : {
    pageChange(start){
      this.paginationStart = start;
      this.paginationEnd = start+this.paginationRange>this.paths.length?this.paths.length:start+this.paginationRange
    }
  }
};
</script>

<style scoped>
</style>