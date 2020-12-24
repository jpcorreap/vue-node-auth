<template>

  <v-app>
    <v-navigation-drawer v-model="sidebar" app>
      <div class="navbar">
      <v-list>
        <v-list-tile>
          <v-list-tile-action>
            <v-icon>{{ menuItems[0]["icon"] }}</v-icon>
          </v-list-tile-action>
          <v-list-tile-content>{{ menuItems[0]["title"] }}</v-list-tile-content>
        </v-list-tile>
      </v-list>
    </div>
    </v-navigation-drawer>
       
    <v-toolbar app>
      <span class="hidden-sm-and-up">
        <v-toolbar-side-icon @click="sidebar = !sidebar">
        </v-toolbar-side-icon>
      </span>
      <v-toolbar-title>
        <router-link to="/" tag="span" style="cursor: pointer">
          {{ appTitle }}
        </router-link>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-toolbar-items class="hidden-xs-only">
        <v-btn
          flat
          v-for="item in menuItems"
          :key="item.title"
          :to="item.path">
          <v-icon left dark>{{ item.icon }}</v-icon>
          {{ item.title }}
        </v-btn>
      </v-toolbar-items>
    </v-toolbar>

    <v-content>
      <router-view></router-view>
    </v-content>
    
  </v-app>

</template>

<script>
export default {
  name: "App",
  data(){
    return {
      appTitle: 'J&J',
      sidebar: false,
      menuItems: [
          { title: 'Ingresar', path: '/login', icon: 'lock_open' }
     ]
    }
  },
};
</script>
<style>
.navbar {
  height: 8vh;
  background-color: black;
}
</style>
