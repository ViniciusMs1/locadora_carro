<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-8">


        <!-- início do card de busca -->
        <card-component titulo="Busca de marcas">
          <template v-slot:conteudo>
            <div class="form-row">
              <div class="col mb-3">
                <input-component titulo="ID" id="inputId" id-help="idHelp"
                  texto-ajuda="Opcional. Informe o ID da marca">
                  <input type="number" class="form-control" id="inputId" aria-describedby="idHelp" placeholder="ID"
                    v-model="busca.id">
                </input-component>
              </div>
              <div class="col mb-3">
                <input-component titulo="Nome da marca" id="inputNome" id-help="nomeHelp"
                  texto-ajuda="Opcional. Informe o nome da marca">
                  <input type="text" class="form-control" id="inputNome" aria-describedby="nomeHelp"
                    placeholder="Nome da marca" v-model="busca.nome">
                </input-component>
              </div>
            </div>
          </template>

          <template v-slot:rodape>
            <button type="submit" class="btn btn-primary btn-sm float-right" @click="pesquisar()">Pesquisar</button>
          </template>
        </card-component>
        <!-- fim do card de busca -->


        <!-- início do card de listagem de marcas -->
        <card-component titulo="Relação de marcas">
          <template v-slot:conteudo>
            <table-component :dados="marcas.data"
              :visualizar="{ visivel: true, dataToggle: 'modal', dataTarget: '#modalMarcaVisualizar' }"
              :atualizar="{ visivel: true, dataToggle: 'modal', dataTarget: '#modalMarcaAtualizar' }"
              :remover="{ visivel: true, dataToggle: 'modal', dataTarget: '#modalMarcaRemover' }" :titulos="{
                id: { titulo: 'ID', tipo: 'texto' },
                nome: { titulo: 'Nome', tipo: 'texto' },
                imagem: { titulo: 'Imagem', tipo: 'imagem' },
                created_at: { titulo: 'Criação', tipo: 'data' },
              }"></table-component>
          </template>

          <template v-slot:rodape>
            <div class="row">
              <div class="col-10">
                <paginate-component>
                  <li v-for="l, key in marcas.links" :key="key" :class="l.active ? 'page-item active' : 'page-item'"
                    @click="paginacao(l)">
                    <a class="page-link" v-html="l.label"></a>
                  </li>
                </paginate-component>
              </div>

              <div class="col">
                <button type="button" class="btn btn-primary btn-sm float-right" data-toggle="modal"
                  data-target="#modalMarca">Adicionar</button>
              </div>
            </div>
          </template>
        </card-component>
        <!-- fim do card de listagem de marcas -->
      </div>
    </div>


    <!-- INICIO DO MODAL DE ADICIONAR MARCA -->
    <modal-component id="modalMarca" titulo="Adicionar marca">

      <template v-slot:alertas>
        <alert-component tipo="success" :detalhes="transacaoDetalhes" titulo="Cadastro realizado com sucesso"
          v-if="transacaoStatus == 'adicionado'"></alert-component>
        <alert-component tipo="danger" :detalhes="transacaoDetalhes" titulo="Erro ao tentar cadastrar a marca"
          v-if="transacaoStatus == 'erro'"></alert-component>
      </template>

      <template v-slot:conteudo>
        <div class="form-group">
          <input-component titulo="Nome da marca" id="novoNome" id-help="novoNomeHelp"
            texto-ajuda="Informe o nome da marca">
            <input type="text" class="form-control" id="novoNome" aria-describedby="novoNomeHelp"
              placeholder="Nome da marca" v-model="nomeMarca">
          </input-component>
          {{ nomeMarca }}
        </div>

        <div class="form-group">
          <input-component titulo="Imagem" id="novoImagem" id-help="novoImagemHelp"
            texto-ajuda="Selecione uma imagem no formato PNG">
            <input type="file" class="form-control-file" id="novoImagem" aria-describedby="novoImagemHelp"
              placeholder="Selecione uma imagem" @change="carregarImagem($event)">
          </input-component>
          {{ arquivoImagem }}
        </div>
      </template>

      <template v-slot:rodape>
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
        <button type="button" class="btn btn-primary" @click="salvar()">Salvar</button>
      </template>
    </modal-component>
    <!-- FIM DO MODAL DE ADICIONAR MARCA -->


    <!-- INICIO DO MODAL DE VISUALIZAR MARCA -->
    <modal-component id="modalMarcaVisualizar" titulo="Visualizar marca">
      <template v-slot:alertas></template>
      <template v-slot:conteudo>
        <input-component titulo="ID">
          <input type="text" class="form-control" :value="$store.state.item.id" disabled>
        </input-component>
        <input-component titulo="Nome da Marca">
          <input type="text" class="form-control" :value="$store.state.item.nome" disabled>
        </input-component>
        <input-component titulo="Imagem">
          <img :src="'storage/' + $store.state.item.imagem" v-if="$store.state.item.imagem"
            :alt="$store.state.item.nome">
        </input-component>
        <input-component titulo="Data de Cadastro">
          <input type="text" class="form-control" :value="$store.state.item.created_at" disabled>
        </input-component>
      </template>
      <template v-slot:rodape>
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
      </template>
    </modal-component>
    <!-- FIM DO MODAL DE VISUALIZAR MARCA -->



    <!-- INICIO DO MODAL DE EXCLUIR MARCA -->
    <modal-component id="modalMarcaRemover" titulo="Remover marca">
      <template v-slot:alertas>
        <alert-component tipo="success" msg="Transação realizada com sucesso" :detalhes="$store.state.transacao"
          v-if="$store.state.transacao.status == 'sucesso'"></alert-component>
        <alert-component tipo="danger" msg="Erro na transação" :detalhes="$store.state.transacao"
          v-if="$store.state.transacao.status == 'erro'"></alert-component>
      </template>
      <template v-slot:conteudo v-if="$store.state.transacao.status != 'sucesso'">
        <input-component titulo="ID">
          <input type="text" class="form-control" :value="$store.state.item.id" disabled>
        </input-component>
        <input-component titulo="Nome da Marca">
          <input type="text" class="form-control" :value="$store.state.item.nome" disabled>
        </input-component>
      </template>
      <template v-slot:rodape>
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
        <button type="button" class="btn btn-danger" @click="remover()"
          v-if="$store.state.transacao.status != 'sucesso'">Remover</button>
      </template>
    </modal-component>
    <!-- FIM DO MODAL DE EXCLUIR MARCA -->


    <!-- INICIO DO MODAL DE ATUALIZAR MARCA -->
    <modal-component id="modalMarcaAtualizar" titulo="Atualizar marca">

      <template v-slot:alertas>

      </template>

      <template v-slot:conteudo>
        <div class="form-group">
          <input-component titulo="Nome da marca" id="atualizarNome" id-help="atualizarNomeHelp"
            texto-ajuda="Informe o nome da marca">
            <input type="text" class="form-control" id="atualizarNome" aria-describedby="atualizarNomeHelp"
              placeholder="Nome da marca" v-model="$store.state.item.nome">
          </input-component>

        </div>

        <div class="form-group">
          <input-component titulo="Imagem" id="atualizarImagem" id-help="atualizarImagemHelp"
            texto-ajuda="Selecione uma imagem no formato PNG">
            <input type="file" class="form-control-file" id="atualizarImagem" aria-describedby="atualizarImagemHelp"
              placeholder="Selecione uma imagem" @change="carregarImagem($event)">
          </input-component>

        </div>
      </template>

      <template v-slot:rodape>
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Fechar</button>
        <button type="button" class="btn btn-primary" @click="atualizar()">Atualizar</button>
      </template>
    </modal-component>
    <!-- FIM DO MODAL DE ATUALIZAR MARCA -->



  </div>
</template>

<script>
import axios from 'axios'
import Paginate from './Paginate.vue'
export default {
  components: { Paginate },
  data() {
    return {
      urlBase: 'http://localhost:8000/api/v1/marca',
      urlPaginacao: '',
      urlFiltro: '',
      nomeMarca: '',
      arquivoImagem: [],
      transacaoStatus: '',
      transacaoDetalhes: {},
      marcas: { data: [] },
      busca: { id: '', nome: '' }
    }
  },
  methods: {
    atualizar() {
      let url = this.urlBase + '/' + this.$store.state.item.id;
      let formData = new FormData()
      formData.append('_method', 'patch')
      formData.append('nome', this.$store.state.item.nome)
      if (this.arquivoImagem[0]) {
        formData.append('imagem', this.arquivoImagem[0])
      }

      let config = {
        headers: {
          'Content-Type': 'multiform/form-data'
        }
      }
      axios.post(url, formData, config)
        .then(response => {
          atualizarImagem.value = '';
          this.carregarLista()
        }).catch(errors => {

        })
    },
    remover() {
      let confirmacao = confirm('Tem certeza que deseja remover?');
      let url = this.urlBase + '/' + this.$store.state.item.id
      let formData = new FormData()
      formData.append('_method', 'delete');
      if (!confirmacao) return false;

      axios.post(url, formData)
        .then(response => {
          this.$store.state.transacao.status = 'sucesso'
          this.$store.state.transacao.mensagem = response.data.msg
          this.carregarLista()
        })
        .catch(errors => {
          this.$store.state.transacao.status = 'erro'
          this.$store.state.transacao.mensagem = errors.response.data.erro
          console.log('Erro');
        })
    },
    pesquisar() {
      //console.log(this.busca)

      let filtro = ''

      for (let chave in this.busca) {

        if (this.busca[chave]) {
          //console.log(chave, this.busca[chave])
          if (filtro != '') {
            filtro += ";"
          }

          filtro += chave + ':like:' + this.busca[chave]
        }
      }
      if (filtro != '') {
        this.urlPaginacao = 'page=1'
        this.urlFiltro = '&filtro=' + filtro
      } else {
        this.urlFiltro = ''
      }

      this.carregarLista()
    },
    paginacao(l) {
      if (l.url) {
        //this.urlBase = l.url //ajustando a url de consulta com o parâmetro de página
        this.urlPaginacao = l.url.split('?')[1]
        this.carregarLista() //requisitando novamente os dados para nossa API
      }
    },
    carregarLista() {



      let url = this.urlBase + '?' + this.urlPaginacao + this.urlFiltro
      console.log(url)
      axios.get(url)
        .then(response => {
          this.marcas = response.data
          //console.log(this.marcas)
        })
        .catch(errors => {
          console.log(errors)
        })
    },
    carregarImagem(e) {
      this.arquivoImagem = e.target.files
    },
    salvar() {
      console.log(this.nomeMarca, this.arquivoImagem[0])

      let formData = new FormData();
      formData.append('nome', this.nomeMarca)
      formData.append('imagem', this.arquivoImagem[0])

      let config = {
        headers: {
          'Content-Type': 'multipart/form-data',
        }
      }

      axios.post(this.urlBase, formData, config)
        .then(response => {
          this.transacaoStatus = 'adicionado'
          this.transacaoDetalhes = {
            mensagem: 'ID do registro: ' + response.data.id
          }

          console.log(response)
          this.carregarLista()
        })
        .catch(errors => {
          this.transacaoStatus = 'erro'
          this.transacaoDetalhes = {
            mensagem: errors.response.data.message,
            dados: errors.response.data.errors
          }
          //errors.response.data.message
        })
    }
  },
  mounted() {
    this.carregarLista()
  }
}
</script>
