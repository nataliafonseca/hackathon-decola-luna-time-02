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
        <h1 class="titulo text-center mb-1 mt-5">
          {{ informacao.nome }}
        </h1>
        <h4 class="text-h7 text-center mb-3 endereco-h4">
          {{ informacao.endereco }} - CEP {{ informacao.cep | formataCep }}
          <v-icon color="green darken-1"> mdi-map-marker </v-icon>
        </h4>
        <v-divider></v-divider>
        <h2 class="text-h5 text-center mb-3 mt-5">Nossos Ovos</h2>

        <div v-for="produto in produtos" :key="produto.id">
          <v-card max-width="375" class="mx-auto mb-6 card-produtos" >
            <v-img :src="produto.imagem" height="300px" imagem-produto dark>
              <v-row class="fill-height">
                <v-spacer></v-spacer>

                <v-card-title class="white--text pl-12 pt-12"> </v-card-title>
              </v-row>
            </v-img>

            <v-list two-line>
              <v-list-item>
                <h4 class="text-h4 pl-12 pt-12 produto-h4">
                  {{ produto.nome }}
                </h4>
              </v-list-item>

              <v-divider inset></v-divider>

              <v-divider inset></v-divider>

              <v-list-item>
                <v-list-item-content>
                  <v-list-item-title class="sabor-produto">{{
                    produto.sabor
                  }}</v-list-item-title>

                  <v-list-item-subtitle class="preco-produto"
                    >Preço:
                    {{ produto.preco | formatarPreco }}</v-list-item-subtitle
                  >
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-card>
        </div>
      </v-container>
    </transition>
  </div>
</template>

<script>
export default {
  name: "PaginaLoja",
  props: ["id"],
  data() {
    return {
      produtos: [],
      informacao: null,
      carregando: false,
    };
  },
  filters: {
    formatarPreco(valor) {
      return valor.toLocaleString("pt-BR", {
        style: "currency",
        currency: "BRL",
      });
    },
    formataCep(valor) {
      var str = valor.slice(0, 5) + "-" + valor.slice(5);
      return str;
    },
  },
  methods: {
    fetchInformation(id) {
      this.carregando = true;
      this.produtos = [];
      this.informacao = null;
      fetch("https://it3-hbn-default-rtdb.firebaseio.com/ovosPascoa.json")
        .then((response) => response.json())
        .then((json) => {
          json.forEach((item) => {
            if (Number(item.local.id) === Number(id)) {
              this.produtos.push(item);
              if (!this.informacao) {
                this.informacao = item.local;
              }
            }
          });
          this.carregando = false;
        });
    },
  },
  created() {
    this.$store.dispatch("redirectLogin");
    this.fetchInformation(this.id);
  },
  beforeRouteUpdate(to, from, next) {
    console.log(to, from);
    this.fetchInformation(to.params.id);
    next();
  },
};
</script>

<style scoped>
.endereco-h4 {
  font-weight: normal;
  border-radius: 10px;
}

.produto-h4 {
  font-weight: light;
  text-align: center;
}

.preco-produto {
  font-weight: bolder;
  font-size: 14px;
}

.sabor-produto {
  font-weight: bolder;
  font-size: 18px;
}
</style>
