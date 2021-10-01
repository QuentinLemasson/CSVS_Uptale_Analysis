<template>
    <button @click='$emit("pathChange", path.id)' id=pathbutton>
        Path #{{path.id}} 
    </button>
</template>

<script>

export default {
  name: "PathButton",
  props: {
    path: Object
  },
  emits: ['pathChange']
};
</script>

<style scoped>

#pathbutton{
    width : 100px;
}

</style>