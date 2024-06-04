<script setup>
</script>

<template>
  <v-card>
    <v-layout>
      <v-app-bar color="pink-lighten-2" title="Sistema"></v-app-bar>
      <v-navigation-drawer
        color="pink-lighten-1"
        v-model="drawer"
        :rail="rail"
        permanent
        @click="rail = false"
      >
        <v-list-item
          prepend-icon="mdi-menu"
          title="Menu"
          nav
        >
          <template v-slot:append>
            <v-btn
              variant="text"
              icon="mdi-chevron-left"
              @click.stop="rail = !rail"
            ></v-btn>
          </template>
        </v-list-item>

        <v-divider></v-divider>

        <v-list density="compact" nav>
          <v-list-item prepend-icon="mdi-home-city" link to="/" title="Inicio"></v-list-item>
          <v-list-item prepend-icon="mdi-tooltip-check" link to="/activos" title="Activos"></v-list-item>
          <v-list-item prepend-icon="mdi-account-multiple" link to="/responsables" title="Responsables"></v-list-item>
          <v-list-item prepend-icon="mdi-map-marker-radius" link to="/ubicaciones" title="Ubicaciones"></v-list-item>
          <v-list-item prepend-icon="mdi-tag-multiple" link to="/tags" title="Tags"></v-list-item>
          <v-list-item prepend-icon="mdi-tag-check-outline" link to="/activotags" title="Activo Tags"></v-list-item>
        </v-list>
      </v-navigation-drawer>
      
      <v-main class="d-flex align-center justify-center" style="min-height: 730px;"><router-view></router-view></v-main>
    </v-layout>
  </v-card>
</template>
<script>
  export default {
    data () {
      return {
        drawer: true,
        rail: true,
      }
    },
  }
</script>