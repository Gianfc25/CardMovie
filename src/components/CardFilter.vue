<template>
  <v-container  class="d-flex justify-center align-center" style="height: 100vh;">
   <v-card height="800" width="800">
    <v-container>
      <div style="margin-bottom: 20px;font-size: 20px;padding-left: 16px;">Selecciona el filtro correspondiente de la lista </div>
      <v-row style="padding-left: 16px;">
        <v-col cols="4">
          <v-select
            v-model="selectedGenre"
            :items="genres"
            label="Género"
          ></v-select>
        </v-col>
        <v-col cols="4">
          <v-text-field
            v-model="searchName"
            label="Nombre"
            clearable
          ></v-text-field>
        </v-col>
        <v-col cols="4">
          <v-btn color="primary" @click="clearFilters">Limpiar filtros</v-btn>
        </v-col>
        <v-col cols="8">
          <v-text-field
            v-model="searchDescription"
            label="Descripción"
            clearable
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-list class="ma-2">
            <v-list-item
              v-for="movie in filteredMovies"
              :key="movie.id"
            >
              <v-list-item-title style="margin-bottom: 8px; font-size: 20px; text-decoration: underline;" >{{ movie.title }}</v-list-item-title>
              <v-list-item-subtitle >{{ movie.genre }}</v-list-item-subtitle>
              <v-div style="font-size: 16px" >{{ movie.description }}</v-div>
            </v-list-item>
          </v-list>
        </v-col>
      </v-row>
    </v-container>
   </v-card>
  </v-container>
</template>

<script>
import { ref, computed } from 'vue';
export default {
  setup() {
    const movies = ref([]);
    const genres = ref([]);
    const selectedGenre = ref(null);
    const searchName = ref('');
    const searchDescription = ref('');

    const fetchMovies = async () => {
      try {
        const response = await fetch('https://667385a16ca902ae11b4711a.mockapi.io/movies');
        const data = await response.json();
        movies.value = data;
        const genresSet = new Set(data.map(movie => movie.genre));
        genres.value = Array.from(genresSet);
      } catch (error) {
        console.error(error);
      }
    };

    const filteredMovies = computed(() => {
      return movies.value.filter(movie =>
        (!selectedGenre.value || movie.genre === selectedGenre.value) &&
        (!searchName.value || movie.title.toLowerCase().includes(searchName.value.toLowerCase())) &&
        (!searchDescription.value || movie.description.toLowerCase().includes(searchDescription.value.toLowerCase()))
      );
    });

    const clearFilters = () => {
      selectedGenre.value = null;
      searchName.value = '';
      searchDescription.value = '';
    };

    fetchMovies();

    return {
      movies,
      genres,
      selectedGenre,
      searchName,
      searchDescription,
      filteredMovies,
      clearFilters,
    };
  },
};
</script>