<template>
  <div>
    <transition>
      <v-container v-if="carregando" class="text-center mt-6" key="loading">
        <v-progress-circular
          color="#1B5E20"
          indeterminate
        ></v-progress-circular>
      </v-container>
      <v-container v-else key="element">
        <h1 class="titulo text-center mb-3 mt-5">iPáscoa</h1>
        <p class="text-center font-weight-bold">
          Compre ovos de Páscoa para você ou para presentear alguém de maneira
          rápida e fácil!
        </p>
        <p class="text-center font-weight-bold">
          Além de deixar o dia de alguém mais feliz, você ainda ajuda os
          revendedores da sua região! Se você for um revendedor e quiser
          disponibilizar seus dados no site, basta se cadastrar!
          <br />
        </p>
        <v-divider></v-divider>
        <v-spacer></v-spacer>
        <p class="text-center text-h6">Confira nossas lojas:</p>
        <div v-for="loja in lojas" :key="loja.id">
          <v-card
            @click="$router.push(`/loja/${loja.id}`)"
            max-width="375"
            class="mx-auto mb-6"
          >
            <v-img
              src="https://www.pngitem.com/pimgs/m/300-3000637_easter-bunny-easter-egg-easter-eggs-in-a.png"
              height="300px"
              dark
            >
              <v-row class="fill-height">
                <v-spacer></v-spacer>

                <v-card-title class="white--text pl-12 pt-12"> </v-card-title>
              </v-row>
            </v-img>

            <v-list two-line>
              <v-list-item>
                <h4 class="text-h4 pl-12 pt-12">{{ loja.nome }}</h4>
              </v-list-item>

              <v-divider inset></v-divider>

              <v-divider inset></v-divider>

              <v-list-item>
                <v-list-item-icon>
                  <v-icon color="green darken-1"> mdi-map-marker </v-icon>
                </v-list-item-icon>

                <v-list-item-content>
                  <v-list-item-title class="text-wrap">{{
                    loja.endereco
                  }}</v-list-item-title>

                  <v-list-item-subtitle
                    >CEP: {{ loja.cep | formataCep }}</v-list-item-subtitle
                  >
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-card>
        </div>
      </v-container></transition
    >
  </div>
</template>

<script>
export default {
  name: "PaginaInicio",
  data() {
    return {
      lojas: [],
      carregando: false,
    };
  },
  filters: {
    formataCep(valor) {
      var str = valor.slice(0, 5) + "-" + valor.slice(5);
      return str;
    },
  },
  created() {
    this.$store.dispatch("redirectLogin");
    this.carregando = true;
    fetch("https://it3-hbn-default-rtdb.firebaseio.com/ovosPascoa.json")
      .then((response) => response.json())
      .then((json) => {
        json.forEach((item) => {
          if (
            this.lojas.filter((loja) => loja.id === item.local.id).length === 0
          ) {
            this.lojas.push(item.local);
          }
          this.carregando = false;
        });
      });
  },
};
</script>

<style scoped>
.titulo {
  color: #1b5e20;
}
</style>
