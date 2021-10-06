<template>
  <div :key="path.id" v-for="path in paginate()">
    <Path :path="path" :display="display" :categories="categories" />
  </div>
</template>

<script>
import Path from "./Path.vue";

export default {
  name: "Paths",
  components: {
    Path,
  },
  props: {
    paths: Array,
    display: String,
    categories: Map,
    start: {
      type : Number,
      default : 0
    },
    end: {
      type : Number,
      default : -1
    }
  },
  methods : {
    paginate(){
      if(this.end<this.start){
        return this.paths;
      }
      return this.paths.slice(this.start, this.end);
    }
  }
  
};
</script>

<style scoped>
</style>