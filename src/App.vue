<template>
  <v-app>

    <!-- Хедер -->
    <v-app-bar app elevation="1" color="cyan darken-1" height="64">
      <div class="page-wrapper">
        <v-container fluid>
          <v-row>
            <v-col class="py-0 d-flex align-center">
              <a href="/" v-if="$vuetify.breakpoint.mdAndUp" class="d-flex align-start text-decoration-none">
                <v-img max-height="30" max-width="30" src="./assets/logo.png" class="align-self-center"></v-img>
                <h2 class="text-h6 pl-3 white--text d-flex align-center">Название название</h2>
              </a>
              <v-chip v-if="$vuetify.breakpoint.mdAndUp" small label class="ml-3 text-caption grey lighten-4">alpha</v-chip>
              <v-switch v-model="$vuetify.theme.dark" inset class="ml-4 themeSwitch"></v-switch>
            </v-col>
          </v-row>
        </v-container>
      </div>
    </v-app-bar>

    <!-- Горизонтальное меню -->
    <v-app-bar app flat style="top: 64px;" color="white" height="46">
      <div class="page-wrapper fill-height">
        <v-toolbar-items>
          <v-btn 
            v-for="item in menuItems" :key="item.title" :to="item.path" text
            :ripple="false" color="grey darken-2"  
            class="navItem" active-class="navItemActive"
          >
            <v-icon left dark>{{ item.icon }}</v-icon>
            <span>{{ item.title }}</span>
          </v-btn>
        </v-toolbar-items>
      </div>
    </v-app-bar>

    <!-- <v-navigation-drawer v-model="sidebar" app>
      <v-list>
        <v-list-tile
          v-for="item in menuItems"
          :key="item.title"
          :to="item.path">
          <v-list-tile-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-tile-action>
          <v-list-tile-content>{{ item.title }}</v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer> -->

    <!-- <v-app-bar app color="cyan darken-1">

      <span class="hidden-sm-and-up">
        <v-toolbar-side-icon @click="sidebar = !sidebar">
        </v-toolbar-side-icon>
      </span>

      <v-toolbar-title>
        <router-link to="/" tag="span" style="cursor: pointer">
          {{ appTitle }}
        </router-link>
      </v-toolbar-title>

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

    </v-app-bar> -->



    <v-main app :class="$vuetify.theme.dark ? '' : 'page-background'">
      <div class="page-wrapper">
        <router-view/>
      </div>
      
    </v-main>


  </v-app>
</template>

<script>

export default {
  name: 'App',

  data: () => ({
    sidebar: false,
    menuItems: [
        { title: 'Главная', path: '/', icon: 'mdi-home-outline' },
        { title: 'Компоненты', path: '/components', icon: 'mdi-memory' },
        { title: 'Производители', path: '/brands', icon: 'mdi-shopping-outline' }
    ]
  }),
};
</script>

<style scoped>
>>>.v-toolbar__content, >>>.v-toolbar__extension {
  padding: 0;
}
.page-wrapper {
  /* flex-basis: 1300px; */
  /* max-width: 1300px; */
  /* margin: auto; */
  margin: 0 20px;
}
.page-background {
  background: #eef5f9;
}

/* .page-background {
  background: linear-gradient(#24778a, #484c7b);
} */

>>>.themeSwitch .v-input__control .v-messages { /* Убирает блок сообщений у перелючателя темы */
  display: none !important;
}
>>>.themeSwitch .v-input__control .v-input__slot { /* Убирает марджины у перелючателя темы */
  margin: 0;
}


>>>.v-btn--active.v-btn--text.navItem::hover::before { /* Убирает цвет фона выбранной кнопки */
  opacity: 0;
}

>>>.v-btn--active.v-btn--text.navItem::before { /* Убирает цвет фона выбранной кнопки */
  opacity: 0;
}

>>>.v-btn--active.navItem.navItemActive {
  color: #212121 !important;
}
</style>