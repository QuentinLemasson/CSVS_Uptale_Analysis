<template>
    <div id=content>
        <PathSelector @pathChange='changeItem' id=pathSelector class='grid-cell' :data='data'/>
        <PathVisualization id=graph class='grid-cell' :data='actualItem.data'/> 
        <UserList id=users class='grid-cell' :users='actualItem.users'/>
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
    changeItem(key){
      this.actualItem = this.data.find(e=>e.id==key.id&&e.iteration==key.iteration);
    }
  }
};
</script>

<style scoped>

#content{
  height:100%;
  width:100%;
  display:grid;
  grid-gap:  10px;
  grid-template-columns:  1fr 20%;
  grid-template-rows:     min-content 1fr;
}

.grid-cell{
  border: outset 4px black
}

#pathSelector{
  grid-column: 1; 
  grid-row: 1;
}

#graph{
  grid-column: 1; 
  grid-row: 2;
}

#users{
  max-height:894px;
  overflow-y: scroll;
  grid-column: 2; 
  grid-row: 1  / span 2;
}

</style>