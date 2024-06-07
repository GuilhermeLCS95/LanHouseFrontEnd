<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>

    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <img src='../static/lanhouse-icon.png' width="40px"/>
      <h3 id="greetings" v-text="greetingHour"></h3>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>

    <v-footer
      :absolute="!fixed"
      app
    >
      <span>&copy; Desenvolvido por: <a href="https://www.linkedin.com/in/guilherme-lu%C3%ADs-carneiro-da-silva-2b3160252/" target="_blank">Guilherme Silva</a></span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          title: 'Registrar',
          to: '/register'
        },
        {
          title: 'Tabela de registros',
          to: '/'
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Lan house',
    }
  },
  created() {
    this.greetings(); // Chama a função greetings quando o componente é criado
  },
  methods: {
    greetings() {
      let hour = new Date().getHours();
      let greetingHour;
      if (hour > 0 && hour < 5) {
        greetingHour = 'Tenha uma boa madrugada!';
      } else if (hour > 4 && hour < 12) {
        greetingHour = 'Tenha um bom dia!';
      } else if (hour > 11 && hour < 19) {
        greetingHour = 'Tenha uma boa tarde!';
      } else {
        greetingHour = 'Tenha uma boa noite!';
      }
      this.greetingHour = greetingHour;
    }
  }
}
</script>

<style>

#greetings{
  text-align: center;
  width: 100vw;
}

</style>
