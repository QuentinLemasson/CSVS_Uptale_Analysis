<template>
  <header>
    <h1>{{ title }}</h1>
    <FileLoader
      @filesLoaded="filesLoaded"
      :fields="fields"
      :isLoading="isLoading"
      :csvData="csvData"
      :perUserScores="perUserScores"
    />
  </header>
</template>

<script>
import FileLoader from "./FileLoader.vue";

export default {
  name: "Header",
  components: { FileLoader },
  emits: ["filesLoaded"],
  data() {
    return {};
  },
  props: {
    title: {
      type: String,
      default: "[Insert Title here]",
    },
    perUserScores: Object,
    csvData: String,
    isLoading: Number,
    fields: {},
  },
  methods: {
    filesLoaded(data) {
      this.$emit("filesLoaded", data);
    },
  },
};
</script>

<style scoped>
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}
</style>
