<template>
  <div class="container">
    <h1>Lista Pokemon</h1>

    <ListaPokemons 
      :imageUrl="imageUrl" 
      :apiUrl="apiUrl"
      @setPokemonUrl="setPokemonUrl" />
    <DetalhesPokemon 
      v-if="showDetail"
      :pokemonUrl="pokemonUrl"
      :imageUrl="imageUrl"
      @closeDetail="closeDetail" />
  </div>
</template>

<script>
  import BuscaPokemons from './BuscaPokemons.vue';
  import ListaPokemons from './ListaPokemons.vue';
  import DetalhesPokemon from './DetalhesPokemon.vue';

  export default {
    data: () => {
      return {
        imageUrl: 'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/',
        apiUrl: 'https://pokeapi.co/api/v2/pokemon/',
        pokemonUrl: '',
        showDetail: false
      }
    },
    components: {
      BuscaPokemons,
      ListaPokemons,
      DetalhesPokemon
    },
    methods: {
      setPokemonUrl(url) {
        this.pokemonUrl = url;
        this.showDetail = true;
      },
      closeDetail() {
        this.pokemonUrl = '';
        this.showDetail = false;
      }
    }
  }
</script>

<style lang="css" scoped>

  .container {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    padding: 10px;
    width: calc(100% - 20px);
    min-height: calc(100vh - 20px);
    background: #ccc;

    font-family: 'Acme', arial;
    font-size: 1rem;
    font-weight: normal;
  }

  h1 { color: #000; }
</style>
