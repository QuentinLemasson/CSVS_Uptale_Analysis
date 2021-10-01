<template>
    <div id=content>
        <PathSelector @pathChange='changeItem' id=pathSelector :data='data'/>
        <PathVisualization id=graph :data='actualItem.data'/> 
        <div id=users></div>
    </div>
</template>

<script>
import PathSelector from "./PathSelector.vue";
import UserList from "./UserList.vue";
import PathVisualization from "./PathVisualization.vue";

export default {
  name: "DataByPath",
  components: {
    PathSelector,
    UserList,
    PathVisualization
  },
  props: {
    data: Array
  },
  data() {
    return {
      actualItem : Object
    }
  },
  created() {
    this.actualItem=this.data[0];
  },
  methods: {
    changeItem(id){
      console.log(id)
      this.actualItem = this.data.find(e=>e.id==id);
    }
  }
};
</script>

<style scoped>

#content{
  height:100%;
  width:100%;
  display:grid;
  grid-template-columns:  1fr min-content;
  grid-template-rows:     min-content 1fr;
}

#pathSelector{
  grid-column: 1; 
  grid-row: 1;
  background-color: chartreuse;
}

#graph{
  grid-column: 1; 
  grid-row: 2;
  background-color: rgb(210, 30, 180);
}

#users{
  width:150px;
  grid-column: 2; 
  grid-row: 1  / span 2;
  background-color: darkgoldenrod;
}

</style>