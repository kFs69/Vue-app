<template>
  <div>
    <Navbar/>
    <v-alert
      class="alert"
      v-model="alert"
      color="error"
      transition="slide-x-reverse-transition"
      dark
    >
      Deseja mesmo excluir esse registro?
      <v-row>
        <v-col class="shrink">
          <v-btn @click="deletar(registroID); alert = false; reload();">Excluir</v-btn>
        </v-col>
        <v-col class="shrink">
          <v-btn @click="alert = false">Cancelar</v-btn>
        </v-col>
      </v-row>  
    </v-alert>
    <v-alert
      class="alert"
      v-model="alertSuccess"
      color="success"
      dismissible
      transition="slide-x-reverse-transition"
      dark
    >
      {{AddAlertSuccess ? 'Registro criado com sucesso!' : EditAlertSuccess ? 'Registro editado com sucesso' : 'Registro deletado com sucesso!'}} 
    </v-alert>
    <v-row justify="center" style="padding-top: 20px">
      <v-dialog v-model="dialog" persistent max-width="600px">
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            color="primary"
            dark
            v-bind="attrs"
            v-on="on"
          >
            Adicionar Filme
          </v-btn>
        </template>
        <v-card>
          <v-card-title>
            <span class="headline">Adicionar Filme</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12" sm="8" md="6">
                  <v-text-field 
                    label="Nome*"
                    value="NOME"
                    id="nome_field"
                    :rules="fieldRules"
                    v-model="nome"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="8" md="6">
                  <v-text-field
                    label="Gênero*"
                    id="genero_field"
                    :rules="fieldRules"
                    v-model="genero"
                    hint="exemplo: Ação, Fantasia, Ficção científica"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <v-text-field
                    label="Elenco*"
                    id="elenco_field"
                    :rules="fieldRules"
                    v-model="elenco"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <v-text-field
                    label="Diretores*"
                    id="diretor_field"
                    :rules="fieldRules"
                    v-model="diretor"
                    required
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                  <v-menu
                    v-model="menu2"
                    :close-on-content-click="false"
                    :nudge-right="40"
                    transition="scale-transition"
                    offset-y
                    min-width="290px"
                  >
                    <template v-slot:activator="{ on, attrs }">
                      <v-text-field
                        v-model="ano"
                        label="Data do lançamento*"
                        readonly
                        id="ano_field"
                        v-bind="attrs"
                        v-on="on"
                        :rules="fieldRules"
                        required
                      ></v-text-field>
                    </template>
                    <v-date-picker v-model="ano" :rules="fieldRules" @input="menu2 = false"></v-date-picker>
                  </v-menu>
                </v-col>
              </v-row>
            </v-container>
            <small>*indicador de campos obrigatórios</small>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="dialog = false; edit = false; resetFields()">Close</v-btn>
            <v-btn color="blue darken-1" text @click="edit ? editM(registroID) : adicionar()">Save</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-row>
    <v-row justify="center">
    <div
      v-for="movie in movies"
      :key="movie.id"
      style="padding: 16px 16px"
    >
      <v-card
        class="mx-auto"
        max-width="300"
        height="458"
      >
        <v-img
          class="white--text align-end"
          height="200px"
          src="https://cdn.vuetifyjs.com/images/cards/docks.jpg"
        >
          <v-card-title>{{ movie.nome }}</v-card-title>
        </v-img>
          <div style="block">
            <v-row>
              <v-card-subtitle class="pb-0">Gênero: {{movie.genero}}</v-card-subtitle>
            </v-row>
            <v-row>
              <v-card-subtitle class="pb-0">Elenco: {{movie.elenco}}</v-card-subtitle>
            </v-row>
            <v-row>
              <v-card-subtitle class="pb-0">Diretor: {{movie.diretor}}</v-card-subtitle>
            </v-row>
            <v-row>
              <v-card-subtitle class="pb-0">Ano: {{format_date(movie.ano, true)}}</v-card-subtitle>
            </v-row>
          </div>
        <v-card-actions>
          <v-row justify="center">
            <v-btn
              color="orange"
              text
              @click="edit = true; show(movie._id); registroID = movie._id; dialog = true"
            >
              Editar
            </v-btn>

            <v-btn
              color="orange"
              text
              @click="alert = true; registroID = movie._id"
            >
              Deletar
            </v-btn>
          </v-row>
        </v-card-actions>
      </v-card>
    </div>  
    </v-row>
  </div>  
</template>
<style scoped>
  .alert{
    position: fixed; 
    right: 5px; 
    bottom: 5px; 
    max-width: 500px; 
    width: calc(100% - 10px); 
    z-index: 1000000;
  }
</style>
<script>
import api from '../plugins/apisauce';
import moment from 'moment';
import Navbar           from '@/components/Navbar.vue';

export default {
  name: 'Home',
  components: {
    Navbar
  },
  data: () => ({
    fieldRules: [
      r => (r && r.length > 0) || "Preencha esse campo."
    ],
    movies: [],
    specificMovie: null,
    alert: false,
    alertSuccess: false,
    AddAlertSuccess: false,
    EditAlertSuccess: false,
    edit: false,
    registroID: null,
    dialog: false,
    menu2: false,
    deleteOff: JSON.parse(localStorage.getItem('delete')),
    updateOff: JSON.parse(localStorage.getItem('update')),
    createOff: JSON.parse(localStorage.getItem('create')),
    nome: null,
    genero: null,
    elenco: null,
    diretor: null,
    ano: null
  }),
  methods: {
    resetFields() {
      this.nome = null;
      this.genero = null;
      this.elenco = null;
      this.diretor = null;
      this.ano = null;
    },
    editM(id) {
      api.put(`/movie/${id}`, {
        nome: this.nome,
        genero: this.genero,
        elenco: this.elenco,
        diretor: this.diretor,
        ano: this.ano
      }).then(r => {
        if(r.ok) {
          this.nome = null;
          this.genero = null;
          this.elenco = null;
          this.diretor = null;
          this.ano = null;
          this.alertSuccess = true;
          this.EditAlertSuccess = true;
          this.dialog = false;
          this.reload();
        } else {
          this.updateOff.push({
            _id: id, 
            nome: this.nome,
            genero: this.genero,
            elenco: this.elenco,
            diretor: this.diretor,
            ano: this.ano
          })
          localStorage.setItem('update', JSON.stringify(this.updateOff));
          this.reload();
        }
      })
    },
    show(id) {
      api.get(`/movie/${id}`).then(result => {
        if(result.ok) {
          this.nome = result.data.nome;
          this.genero = result.data.genero;
          this.elenco = result.data.elenco;
          this.diretor = result.data.diretor;
          this.ano = this.format_date(result.data.ano, false);
        } else {
          const index = this.movies.findIndex(x => x._id === id);
          this.nome = this.movies[index].nome;
          this.genero = this.movies[index].genero;
          this.elenco = this.movies[index].elenco;
          this.diretor = this.movies[index].diretor;
          this.ano = this.format_date(this.movies[index].ano, false);
        }
      }) 
    },
    adicionar() {
      if(!this.nome)      return document.getElementById("nome_field").focus();
      if(!this.genero)    return document.getElementById("genero_field").focus();
      if(!this.elenco)    return document.getElementById("elenco_field").focus();
      if(!this.diretor)    return document.getElementById("elenco_field").focus();
      if(!this.ano)    return document.getElementById("ano_field").focus();

      api.post("/movie", {
        nome: this.nome,
        genero: this.genero,
        elenco: this.elenco,
        diretor: this.diretor,
        ano: this.ano
      }).then(result => {
        if(result.ok) {
          this.AddAlertSuccess = true;
          this.alertSuccess = true;
          this.dialog = false
          this.reload();
        } else {
          this.createOff.push({
            nome: this.nome,
            genero: this.genero,
            elenco: this.elenco,
            diretor: this.diretor,
            ano: this.ano
          });
          localStorage.setItem('create', JSON.stringify(this.createOff));
          this.movies.push({
            nome: this.nome,
            genero: this.genero,
            elenco: this.elenco,
            diretor: this.diretor,
            ano: this.ano
          })
          this.reload();
        }
      })
    },
    reload() {
      api.get("/movie").then(result => {
        if(result.ok) {
          this.movies = result.data;
          localStorage.setItem('movies', JSON.stringify(result.data));
        } else {
          this.movies = JSON.parse(localStorage.getItem('movies'));
        }
      })
    },
    deletar(id) {
      api.delete(`/movie/${id}`).then(r => {
        if(r.ok){
          this.alertSuccess = true
        } else {
          this.deleteOff.push(id);
          localStorage.setItem('delete', JSON.stringify(this.deleteOff));
          const index = this.movies.indexOf(id);
          this.movies.splice(index, 1);
        }
      })
    },
    format_date(value, bool){
      if (value && bool) {
        return moment(String(value)).format('DD/MM/YYYY')
      } else if(value && !bool) {
        return moment(String(value)).format('YYYY-MM-DD')
      }
    },
  },
  async created() {
    const token = localStorage.getItem('token');
    api.setHeader('Authorization', `Bearer ${token}`)
    api.get("/movie").then(result => {
      if(result.ok) {
        this.movies = result.data;
        localStorage.setItem('movies', JSON.stringify(result.data));
      } else {
        this.movies = JSON.parse(localStorage.getItem('movies'));
        if(!localStorage.getItem('create'))
          localStorage.setItem('create', JSON.stringify([]))
        if(!localStorage.getItem('update'))
          localStorage.setItem('update', JSON.stringify([]))
        if(!localStorage.getItem('delete'))
          localStorage.setItem('delete', JSON.stringify([]))
        this.updateOff = JSON.parse(localStorage.getItem('update'))
        this.deleteOff = JSON.parse(localStorage.getItem('delete'))
        this.createOff = JSON.parse(localStorage.getItem('create'))
      }
    })
    setInterval(async () => {
      if(this.deleteOff || this.updateOff || this.createOff) {
        await this.createOff.map(item => {
          api.post("/movie", {
            nome: item.nome,
            genero: item.genero,
            elenco: item.elenco,
            diretor: item.diretor,
            ano: item.ano
          }).then(result => {
            if(result.ok) {
              localStorage.setItem('create', JSON.stringify([]))
              this.reload();
            }
          })
        })

        await this.updateOff.map(item => {
          api.put(`/movie/${item._id}`, {
            nome: item.nome,
            genero: item.genero,
            elenco: item.elenco,
            diretor: item.diretor,
            ano: item.ano
          }).then(r => {
            if(r.ok) {
              localStorage.setItem('update', JSON.stringify([]))
              this.reload();
            }
          })
        })

        await this.deleteOff.map(item => {
          api.delete(`/movie/${item}`).then(r => {
            if(r.ok) {
              localStorage.setItem('delete', JSON.stringify([]))
              this.reload();
            }
          })
        })
      }
    }, 5000)
  } 
}
</script>
