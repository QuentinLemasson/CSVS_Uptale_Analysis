<template>
    <button @click='$emit("pathChange", {id : path.id, iteration : path.iteration})' :id='id' class=button>
        Path #{{path.id}} - Iteration {{path.iteration}} 
    </button>
</template>

<script>

export default {
  name: "PathButton",
  props: {
    path: Object,
    id: String
  },
  emits: ['pathChange']
};
</script>

<style scoped>

#pathbutton{
    width : 100px;
}

.active{
  background-color: lightgreen;
}

</style>