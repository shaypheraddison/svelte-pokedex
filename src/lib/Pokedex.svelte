<script>
  export let pokemon = "";

  let pokemonData;
  let pokemonTypes = [];
  let pokemonTypeRelations = {};

  async function fetchPokemonData() {
      const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon.toLowerCase()}/`);
      pokemonData = await response.json();

      pokemonTypes = [];
      for (const typeName of pokemonData.types) {
        pokemonTypes.push(typeName.type.name);
        pokemonTypes = pokemonTypes;
      };

      await damageRelations();

      pokemon = "";
  }

  async function damageRelations() {
    
    for (const type of pokemonTypes) {
      const damageResponse = await fetch(`https://pokeapi.co/api/v2/type/${type.toLowerCase()}/`);
      let typeData = await damageResponse.json();

      pokemonTypeRelations[type] = typeData.damage_relations
    };
  }
    
    
</script>

<main>
  <div class="kalos-dex">
    <img class="top-dex" src="../src/assets/kalosdex-top.png" alt="top-dex">
      <div class="screen">
        <div class="pokemon-search">
          <input class="pokemon-name" type="text" bind:value={pokemon} placeholder="Enter Pokemon Name">
          <button on:click={fetchPokemonData}></button>
        </div>
          <div class="screen-content">
          {#if pokemonData}
            <div>
              <img class="sprite" src="{pokemonData.sprites.front_default}" alt="{pokemonData.name}">
            </div>
            <div class="pokemon-info">
              <h1 class="capitalize">{pokemonData.name}</h1>
              <ul>
                <li class="capitalize">Pokedex Number: {pokemonData.id}</li>
                <li class="capitalize">Type: 
                  {#each pokemonTypes as type}
                    {type + " "}
                  {/each}
                </li>
                {#each Object.keys(pokemonTypeRelations) as damageType}
                <li class="capitalize">Super Effective Against:
                  {#each pokemonTypeRelations[damageType].double_damage_to as superE}
                    {superE.name + " "}
                  {/each}
                </li>
                <li class="capitalize">Weak Against: 
                  {#each pokemonTypeRelations[damageType].double_damage_from as weakA}
                    {weakA.name + " "}
                  {/each}
                </li>
                {/each}
              </ul>
            </div>
          {/if}
        </div>
      </div>
    <img class="bottom-dex" src="../src/assets/kalosdex-bottom.png" alt="bottom-dex">
  </div> 
</main>


<style>
  input {
    position: relative;
    visibility:visible;
  }
  main {
    /* margin-top: -100px; */
    color: black;
  }
  li {
    list-style: none;
  }
  .kalos-dex {
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: aquamarine;
    width: 440px;
    height: 900px;
    border-radius: 50px;
  }

  .pokemon-search {
    margin-top: -120px;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
  }

  .screen {
    display: flex;
    flex-direction: column;
    justify-content: start;
    margin-top: 30%;
    height: 100%;
    width: 100%;
  }

  button {
    margin: 0 10px auto;
    /* padding: 10px; */
    width: 40px;
    height: 40px;
    border-radius: 100px;
    background-color: rgb(0, 153, 255);
  }

  input {
    display: block;
    font-size: large;
    width: 45%;
    padding: 10px;
  }
  input::placeholder {
    text-align: center;
  }

  .screen-content {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
    margin: 0px auto;
    width: 99%;
    height: 100%; 
  }

  .sprite {
    width: 200px;
    height: 200px;
  }

  .capitalize {
    text-transform: capitalize;
  }
  
</style>