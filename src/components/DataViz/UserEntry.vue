<template>

    <div class=userEntry>
        <div> {{user.name}} </div>
        <span @click='collapse' v-if='collapsed'>
        <i class="far fa-plus-square"></i>
        </span>
        <span @click='collapse' v-if='!collapsed'>
        <i class="far fa-minus-square"></i>
        </span>

        
        <div v-show='!collapsed'>
            <div v-for='scene in user.scenes' :key='scene.name'>
                <p>{{scene.name}}</p>
                <div>
                    <p>Zones found</p>
                    <ul>
                        <li v-for='zone in scene.zonesFound' :key='zone.tag'> {{zone.tag}}</li>
                    </ul>
                    <p>Zones scored</p>
                    <ul>
                        <li v-for='zone in scene.zonesScored' :key='zone.tag'> {{zone.tag}}</li>
                    </ul>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
import { CollapseTransition } from "@ivanv/vue-collapse-transition";

export default {
    name: "UserEntry",
        components: {
            CollapseTransition,
        },
    data(){
        return{
            collapsed : true,
        }
    },
    props: {
        user: Object
    },
    methods: {
        collapse(){
            this.collapsed=!this.collapsed
        }
    }
};
</script>

<style scoped>
.userEntry{
    background-color: lightgray;
    border-radius: 10px;
    border: solid 3px gray;
    margin: 8px 4px 8px 4px;
}

.expand-enter-active {
  transition: height 1s;
}
.expand-leave-active {
  transition: height 1s;
}
.expand-enter-from,
.expand-leave-to {
  height: 0px;
}
</style>