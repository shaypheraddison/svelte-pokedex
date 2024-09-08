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
    
    pokemonTypeRelations = {};
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
        <button class="start-btn"></button>
        <div class="pokemon-search">
          <input class="pokemon-name" type="text" bind:value={pokemon} placeholder="Enter Pokemon Name">
          <button class="search-btn" on:click={fetchPokemonData}></button>
        </div>
          <div class="screen-content">
          {#if pokemonData}
            <div>
              <img class="sprite" src="{pokemonData.sprites.front_default}" alt="{pokemonData.name}">
            </div>
            <div class="pokemon-info">
              <h1 class="capitalize">{pokemonData.name}</h1>
              <h2>#{pokemonData.id}</h2>
              <ul class="grid-list">
                <li class="capitalize type">Type: 
                  {#each pokemonTypes as type}
                    {type + " "}
                  {/each}
                </li>
                {#each Object.keys(pokemonTypeRelations) as damageType}
                <li class="capitalize double-d">Double Damage To:
                  {#each pokemonTypeRelations[damageType].double_damage_to as superE}
                    {superE.name + " "}
                  {/each}
                </li>
                <li class="capitalize half-d">Weak Against: 
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

  h1, h2{
    margin-top: -30px;
  }

  input {
    position: relative;
    visibility:hidden;
  }
  main {
    /* margin-top: -100px; */
    color: black;
  }

  .kalos-dex {
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: aquamarine;
    width: 440px;
    height: 300px;
    border-radius: 50px;
  }

  .pokemon-search {
    margin-top: -70px;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
  }

  .screen {
    display: flex;
    flex-direction: column;
    justify-content: start;
    margin-top: 20%;
    height: 100%;
    width: 100%;
  }

  .search-btn {
    visibility: hidden;
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
    width: 100%;
    height: 100%; 
  }

  .sprite {
    width: 200px;
    height: 200px;
  }

  .capitalize {
    text-transform: capitalize;
  }

  .grid-list {
    margin-top: -10px;
    display: grid;
    grid-template-columns: repeat(autofill, minmax(150px, 1fr));
    grid-template-rows: repeat(3, 1fr);
    list-style: none;
    padding: 0;
  }

  .grid-list li {
    padding: 10px;
    border: 0.5px solid #00a2d369;
    text-align: center;
  }

  .type {
    grid-row: 1 / 2;
    grid-column: 1 / 3;
  }

  .double-d {
    grid-row: 2 / 3;
  }

  .half-d {
    grid-row: 3 / 4;
  }

  .bottom-dex {
    margin-top: -50px;
  }

  .start-btn {
    width: 150px;
    height: 100px;
    margin: -120px auto;
    border-radius: 100px;
  }
  
</style>