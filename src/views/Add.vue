<template>
  <div>
    <Navbar/>
    <v-row justify="center">
    <div
      v-for="movie in movies"
      :key="movie.id"
      style="padding: 16px 16px; margin-top: 30px"
    >
      <v-card
        class="mx-auto"
        max-width="300"
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
              <v-card-subtitle class="pb-0">Ano: {{format_date(movie.ano)}}</v-card-subtitle>
            </v-row>
          </div>
        <v-card-actions>
          <v-row justify="center">
            <v-btn
              color="orange"
              text
            >
              Compartilhar
            </v-btn>

            <v-btn
              color="orange"
              text
            >
              Ver mais
            </v-btn>
          </v-row>
        </v-card-actions>
      </v-card>
    </div>  
    </v-row>
  </div>  
</template>
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
    movies: []
  }),
  methods: {
    format_date(value){
      if (value) {
        return moment(String(value)).format('DD/MM/YYYY')
      }
    },
  },
  async created() {
    const token = localStorage.getItem('token');
    api.setHeader('Authorization', `Bearer ${token}`)
    api.get("/movie").then(result => {
      if(result.ok) {
        this.movies = result.data;
      }
    })
  } 
}
</script>
