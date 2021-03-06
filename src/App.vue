<template>
  <Sidebar @sidebar_click="sidebarManager" />

  <div id="nav" :style="{ 'margin-left': sidebarWidth }">
    <Header id=Header
      title="VR@COVID Paths Analysis"
      :fields="fields"
      @filesLoaded="computeData"
      :isLoading="isLoading"
      :arrScenes="scenes"
      :arrCategories="categories"
      :arrThemes="themes"
      :csvData="csvData"
      :perUserScores="perUserScores"
    />

    <div v-if="display == 'all'">
      <AllRoutes :paths="paths" />
    </div>

    <div v-if="display == 'compute'">
      <ComputedRoutes
        :categories="categories"
        :computedPaths="computedPaths"
        @updateAndComputePaths="updateAndComputePaths"
        @mergeThematic="mergeThematic"
      />
    </div>

    <div v-if="display == 'categories'">
      <SceneList
        :mapScenes="scenes"
        :mapCategories="categories"
        :mapThemes="themes"
        @sceneUpdate="sceneUpdate"
      />
    </div>

    <div id=container v-if="display == 'scorePerPath'">
      <DataByPath
        :data="scorePerPathData"
      />
    </div>
  </div>
</template>

<script>
import ComputedRoutes from "./components/Routes/Computed/ComputedRoutes.vue";
import AllRoutes from "./components/Routes/All/AllRoutes.vue";
import SceneList from "./components/Categories/SceneList.vue";
import DataByPath from "./components/DataViz/DataByPath";

import Sidebar from "./components/Sidebar/Sidebar.vue";
import { sidebarWidth } from "@/components/Sidebar/state";

import Header from "./components/Header/Header.vue";

import { wait, mapToArray } from "./utilities";

export default {
  name: "App",
  components: {
    Header,
    ComputedRoutes,
    AllRoutes,
    Sidebar,
    SceneList,
    DataByPath
  },
  data() {
    return {
      computeWorker: Worker,
      //page to show
      display: "categories",
      //raw general file
      general_usage_output: [],
      //raw detail file
      detail_usage_output: [],
      //an entry per session, list of scenes visited this session
      paths: [],
      //list of reduced and merged paths
      computedPaths: [],
      scorePerPathData: [],

      //data about each scene
      scenes: [],
      //data about each categories
      categories: [],
      //data about each theme
      themes: [],

      perUserScores: [],
      csvData: "",
      isLoading: 0,
      //option for computed paths
      merge_themes: false,
      //data files loaded
      fields: [
        {
          name: "general",
          label: "Select general data file file here",
          defaultPath: "/general.csv",
          format: "csv",
        },
        {
          name: "detail",
          label: "Select detail data file file here",
          defaultPath: "/detail.csv",
          format: "csv",
        },
        {
          name: "categories",
          label: "Select categories and themes data file here",
          defaultPath: "/sceneInfo.json",
          format: "json",
        },
      ],
    };
  },
  setup() {
    return { sidebarWidth };
  },
  created() {

    this.computeWorker = new Worker("/compute.js");
    this.computeWorker.onmessage = (e) => {
      this.isLoading = 2;
      wait(1).then(() => {
        if (e.data.order == "computeData") {
          this.scenes = e.data.scenes;
          this.categories = e.data.categories;
          this.themes = e.data.themes;
          this.paths = e.data.paths;
          this.detail_usage_output = e.data.detail_usage_output;
          this.general_usage_output = e.data.general_usage_output;
          this.computedPaths = e.data.computedPaths;
          this.scorePerPathData = e.data.scorePerPathData;
          this.getPerUserScores();
        } else if (e.data.order == "computePaths") {
          this.computedPaths = e.data.computedPaths;
          this.scorePerPathData = e.data.scorePerPathData;
        } else if (e.data.order == "perUserScores") {
          this.perUserScores = e.data.perUserScores;
        }
        this.isLoading = 0;
      });
    };
  },
  computed (){
    
  },
  methods: {
    updateAndComputePaths() {
      this.isLoading = 1;
      this.updatePathsWhitelist(this.paths);
      //Ugly hack for proxy object cloning
      this.computeWorker.postMessage(
        JSON.parse(
          JSON.stringify({
            order: "computePaths",
            paths: this.paths,
            scenes: mapToArray(this.scenes),
            merge_themes: this.merge_themes,
          })
        )
      );
    },

    mergeThematic(e) {
      this.isLoading = 1;
      this.merge_themes = e;
      //Ugly hack for proxy object cloning
      this.computeWorker.postMessage(
        JSON.parse(
          JSON.stringify({
            order: "computePaths",
            paths: this.paths,
            scenes: mapToArray(this.scenes),
            merge_themes: this.merge_themes,
          })
        )
      );
    },

    extractScorePerPathData(e) {
      this.isLoading = 1;
      //Ugly hack for proxy object cloning
      this.computeWorker.postMessage(
        JSON.parse(
          JSON.stringify({
            order: "extractPathData",
            paths: this.paths,
            scenes: mapToArray(this.scenes)
          })
        )
      );
    },

    sidebarManager(to) {
      this.display = to;
    },

    updatePathsWhitelist(paths) {
      //adapte la whitelist d'un parcours en fonction de la whitelist des catégories
      paths.forEach((p) => {
        p.scenes.forEach((e) => {
          e.whitelisted = this.categories.get(
            this.scenes.get(e.scene).category
          ).whitelisted;
        });
      });
    },

    sceneUpdate(scene) {
      this.scenes.set(scene.id, scene);
      this.updateAndComputePaths();
    },

    computeData(files) {
      this.isLoading = 1;
      this.computeWorker.postMessage({
        order: "computeData",
        files,
        merge_themes: this.merge_themes,
      });
    },

    getPerUserScores() {
      this.computeWorker.postMessage(
        JSON.parse(
          JSON.stringify({
            order: "perUserScores",
            paths: this.paths,
          })
        )
      );
    },
  },
};
</script>

<style>
html{
  height : 100%;
}
body{
  height : 100%;
  margin : 0;
}

#app {
  height : 100%;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  transition: 0.3s ease;
  height : 100%;

  display : grid;
  grid-template-rows: min-content 1fr;
  grid-template-columns: 100%;
}
#Header {
  padding: 1vmin;
  grid-column: 1; 
  grid-row: 1;
}
#container{
  padding: 1vmin;
  grid-column: 1; 
  grid-row: 2;
}
</style>
