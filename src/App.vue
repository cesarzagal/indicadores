<template>
  <v-app>
    <v-main>
<!--      <v-toolbar>
        <v-toolbar-items>
          <router-link to="/">
            <v-btn text>
              Dólar
            </v-btn>
          </router-link>
          <v-divider vertical></v-divider>
          <router-link to="/uf">
            <v-btn text>
              UF
            </v-btn>
          </router-link>
          <v-divider vertical></v-divider>
        </v-toolbar-items>
      </v-toolbar>-->
      <v-card
          class="mx-auto overflow-hidden"
          height="100%"
      >
        <v-app-bar

        >
          <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>

          <v-toolbar-title></v-toolbar-title>
        </v-app-bar>

        <v-navigation-drawer
            v-model="drawer"
            absolute
            temporary
        >
          <v-list
              nav
              dense
          >
            <v-list-item-group
                v-model="group"
                active-class="deep-purple--text text--accent-4"
            >
              <v-list-item to="/" class="text-decoration-none">
                <v-list-item-icon>
                  <v-icon>mdi-cash</v-icon>
                </v-list-item-icon>
                <v-list-item-title>Dólar</v-list-item-title>
              </v-list-item>

              <v-list-item to="/uf" class="text-decoration-none">
                <v-list-item-icon>
                  <v-icon>mdi-currency-usd</v-icon>
                </v-list-item-icon>
                <v-list-item-title>UF</v-list-item-title>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-navigation-drawer>
        <v-container>
          <router-view></router-view>
          <v-dialog
              v-model="loading.estado"
              hide-overlay
              persistent
              width="300"
          >
            <v-card
                :color="loading.color"
                dark
            >
              <v-card-text>
                {{ loading.titulo }}
                <v-progress-linear
                    indeterminate
                    color="white"
                    class="mb-0"
                ></v-progress-linear>
              </v-card-text>
            </v-card>
          </v-dialog>
        </v-container>
      </v-card>

    </v-main>
  </v-app>
</template>

<script>
import {mapState} from 'vuex'

export default {
  name: 'App',
  data: () => ({
    drawer: false,
    group: null,
    items:[
      { text: 'Home',
        disabled: false,
        href: 'home'
      },
      { text: 'Uf',
        disabled: false,
        href: 'uf'
      }
    ]
  }),
  computed:{
    ...mapState(['loading'])
  }
};
</script>
