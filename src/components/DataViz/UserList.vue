<template>
    <div>
        <div id=top>
          Users - {{users.length}}
        </div>

        <UserEntry v-for='user in users' :key='user.name' :user='user'/>
    </div>
</template>

<script>
import UserEntry from './UserEntry.vue'

export default {
  name: "UserList",
  components : {
      UserEntry,
  },
  props: {
    users: Array
  }
};
</script>

<style scoped>

#top{
  position: sticky;
  top: 0px;
  background-color: white;
  border-bottom: solid 3px grey;
  width : 100%;
  height : auto;
}

</style>