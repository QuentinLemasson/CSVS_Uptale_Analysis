<template>
  <div id=barContainer v-on:click.prevent>
    <span v-if='start>min' @click='emitPageChange(0-range)'>
      <i class="fas fa-angle-double-left"></i>
    </span>   
      {{start+1}} - {{start+range<=max?start+range:max}} ({{max}} entries)   
    <span v-if='start+range<max' @click='emitPageChange(range)'>
      <i class="fas fa-angle-double-right"></i>
    </span>
  </div>
</template>

<script>

export default {
  name: "PaginationBar",
  emits: ["pageChange"],
  props: {
    min : Number,
    max : Number,
    start : Number,
    range : Number
  },
  methods : {
    emitPageChange(n){
      this.$emit("pageChange",this.start+n<this.min? this.min : this.startn+n>this.max ? this.start : this.start+n);
      console.log("pageChange")
    }
  }
};
</script>

<style scoped>

#barContainer{
    width : 100%;
    height : auto;
    background-color: lightgray;
    border-top : solid 3px gray;

    position:fixed;
    bottom:0;
    right:0;
}

</style>