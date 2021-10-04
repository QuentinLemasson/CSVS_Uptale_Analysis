<template>
    <div id=buttonContainer>
        <PathButton @pathChange='relayPathChange' v-for='path in data' :key='path.id' :path='path' 
        :id='"pathbutton"+path.id+"-"+path.iteration' />
    </div>
</template>

<script>
import * as d3 from "d3";
import PathButton from './PathButton.vue'

export default {
  name: "PathSelector",
  components : {
      PathButton,
  },
  props: {
    data: Array
  },
  emits: ['pathChange'],
  methods: {
    relayPathChange(key){
      this.$emit("pathChange", key)

      d3.selectAll('.button').attr('class',"button inactive");
      d3.select('#pathbutton'+key.id+"-"+key.iteration).attr('class','button active');

    }
  }
};
</script>

<style scoped>
#buttonContainer{
    display:flex;
    align-items: flex-start;
}
</style>