<template>
  <div>
    <table class="table table-hover">
      {{ $store.state.teste }}
      <thead>
        <tr>
          <th scope="col" v-for="t, key in titulos" :key="key">{{ t.titulo }}</th>
          <th v-if="visualizar.visivel || atualizar.visivel || remover.visivel">Ações</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="obj, chave in dadosFiltrados" :key="chave">
          <td v-for="valor, chaveValor in obj" :key="chaveValor">
            <span v-if="titulos[chaveValor].tipo == 'texto'">{{ valor }}</span>
            <span v-if="titulos[chaveValor].tipo == 'data'">
              {{ '...' + valor }}
            </span>
            <span v-if="titulos[chaveValor].tipo == 'imagem'">
              <img :src="'/storage/' + valor" width="30" height="30">
            </span>
          </td>
          <td v-if="visualizar.visivel || atualizar.visivel || remover.visivel">
            <button @click="setStore(obj)" :data-toggle="visualizar.dataToggle" :data-target="visualizar.dataTarget"
              v-if="visualizar.visivel" class="btn btn-outline-primary btn-small">Visualizar</button>
            <button @click="setStore(obj)" :data-toggle="atualizar.dataToggle" :data-target="atualizar.dataTarget" v-if="atualizar.visivel" class="btn btn-outline-primary btn-small">Atualizar</button>
            <button @click="setStore(obj)" :data-toggle="remover.dataToggle" :data-target="remover.dataTarget" v-if="remover.visivel" class="btn btn-outline-danger btn-small">Remover</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: ['dados', 'titulos', 'visualizar', 'atualizar', 'remover'],
  methods: {
    setStore(obj) {
      this.$store.state.transacao.status = ''
      this.$store.state.transacao.mensagem = ''
      this.$store.state.item = obj
    }
  },
  computed: {
    dadosFiltrados() {

      let campos = Object.keys(this.titulos)
      let dadosFiltrados = []

      this.dados.map((item, chave) => {

        let itemFiltrado = {}
        campos.forEach(campo => {

          itemFiltrado[campo] = item[campo] //utilizar a sintaxe de array para atribuir valores a objetos
        })
        dadosFiltrados.push(itemFiltrado)
      })

      return dadosFiltrados //retorne um array de objetos 
    }
  }
}
</script>
