<template>
    <div>
        <div class="searchbar">
            <input type="text" v-model="search" placeholder="Filtro" />
            <select @change="pageDez">
                <option value="1">20 Páginas</option>            
                <option value="2">Scroll infinito</option>
            
            </select>
        </div>
        <div class="list">
            <article v-for="(pokemon, index) in filteredItems" :key="index" @click="setPokemonUrl(pokemon.url)">
                <h2>{{pokemon.id}}</h2>

                <img :src="imageUrl + pokemon.id + '.png'" width="96" height="96" alt="">
                <h3> {{pokemon.name }}</h3>
            </article>
            <div id="scroll-trigger" ref="infinitescrolltrigger" v-show="spinner">
                <i class="fas fa-spinner fa-spin"></i>
            </div>

       
        </div>

             <div class="carregar">
               <button class="btn btn-primary" @click="next">CARREGAR MAIS</button>
            </div>
    </div>
</template>

<script>
    export default {
        props: [
            'imageUrl',
            'apiUrl'
        ],
        data: () => {
            return {
                pokemons: [],
                nextUrl: '',
                currentUrl: '',
                search: '',
                pageSize: 10,
                spinner: false
            }
        },
        computed: {
            filteredItems() {
                return this.pokemons.filter(pokemon => {
                    return pokemon.name.toLowerCase().includes(this.search.toLowerCase())
                })
            },
        },
        methods: {
            pageDez(event) {
                if (event.target.value == 2) {
                    this.spinner = true;                      
                    this.scrollTrigger();
                  
                } else {
                    location.reload();
                }
            },    
            fetchData() {
                let req = new Request(this.currentUrl);
                fetch(req)
                    .then((resp) => {
                        if (resp.status === 200)
                            return resp.json();
                    })
                    .then((data) => {
                        this.nextUrl = data.next;
                        data.results.forEach(pokemon => {
                            pokemon.id = pokemon.url.split('/')
                                .filter(function (part) {
                                    return !!part
                                }).pop();
                            this.pokemons.push(pokemon);
                        });
                    })
                    .catch((error) => {
                        console.log(error);
                    })
            },
            scrollTrigger() {
                const observer = new IntersectionObserver((entries) => {
                    entries.forEach(entry => {
                        if (entry.intersectionRatio > 0 && this.nextUrl) {
                            this.next();
                        }
                    });
                });
                observer.observe(this.$refs.infinitescrolltrigger);
            },  
         

            next() {
                this.currentUrl = this.nextUrl;
                this.fetchData();
            },
        
            setPokemonUrl(url) {
                this.$emit('setPokemonUrl', url);
            }
        },
        created() {
            this.currentUrl = this.apiUrl;
            this.fetchData();
        },   

    }
</script>

<style lang="scss" scoped>
.carregar {
    text-align: center;
    width: 100%;
    float: left;

    button {
        background: #0A2E50;
        color:#fff;
        border:none;
        padding: 10px;
        font-size: 1rem;
        margin: 30px 0;
        border-radius: 5px;
        cursor: pointer;
    }
}

    .list {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
        grid-gap: 10px;
        width: 100%;
        max-width: 970px;

        article {
            height: 150px;
            background-color: #efefef;
            text-align: center;
            text-transform: capitalize;
            cursor: pointer;
            position: relative;

            h2 {
                position: absolute;
                top: 4px;
                left: 4px;
                margin: 0;
                font-size: 1rem;
            }

            h3 {
                margin: 0;
                font-size: 1rem;
            }
        }
    }

    #scroll-trigger {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 100%;
        height: 150px;
        font-size: 2rem;
        color: #efefef;
    }

    .searchbar {
        position: relative;
        width: 100%;
        max-width: 100%;
        padding-bottom: 20px;

        input,
        select {
            border: none;
            outline: none;
            border-radius: 5px;
            padding: 10px 40px 10px 10px;
            width: 45%;
            font-size: 1rem;
            box-shadow: 0 15px 30px rgba(0, 0, 0, .2),
                0 10px 10px rgba(0, 0, 0, .2);
            float: left;
            margin: 20px 5px;
        }

        i {
            position: absolute;
            top: 10px;
            right: 10px;
            font-size: 1.25rem;
            color: #0A2E50;
            cursor: pointer;
        }
    }
</style>