  <script>
    import TopDex from "./Top-Dex.svelte";
    import BottomDex from "./Bottom-Dex.svelte";
    import whoDat from "../assets/who-dat.png"
    export let pokemon = "";

    let pokemonData;
    let pokemonTypes = [];
    let pokemonTypeRelations = {};
    let pokemonVersion = {};
    let shinySprite = "";
    let isOpened = false;
    let isDisabled = true;
    let errorMessage = "";
    let isShiny = false;

    let openAudio = new Audio("../src/assets/open-dex.mp4");

    function getInputValue(event) {
      pokemon = event.target.value;  //dynamically changing pokemon variable to the value of the input
      isDisabled = pokemon.trim() === ""; // dynamically removing any whitesapce from the value and making sure that there is some data/characters entered as the value of pokemon
    }

    async function fetchPokemonData() {
      errorMessage = "";

      isDisabled = true;

      try {
        const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon.toLowerCase()}/`);

        if (!response.ok) {
          throw new Error("Please enter the correct name for the Pokemon.")
        }

        pokemonData = await response.json();
        shinySprite = pokemonData.sprites.front_shiny;

        pokemonTypes = [];
        for (const typeName of pokemonData.types) {
          pokemonTypes.push(typeName.type.name);
          pokemonTypes = pokemonTypes;
        };

        await damageRelations();

      } catch(error) {
        errorMessage = error.message;
        pokemonData = null;
      }

      console.log(pokemonData);

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
    
    function openDex() {
        isOpened = true;
        openAudio.play();
      }

    function showShiny() {
      isShiny = !isShiny;
    }
      
  </script>

  <main>
    <div class="kalos-dex" class:opened={isOpened}>
      <div class="top-dex"><TopDex /></div>
        <div class="screen">
          <button class="start-btn" on:click={openDex}></button>
            <form class="pokemon-search">
              <input type="text" bind:value={pokemon} on:input={getInputValue} placeholder="Enter Pokemon Name">
              <button class="search-btn" on:click={fetchPokemonData} disabled={isDisabled}></button>  
            </form>
            {#if errorMessage}
              <div class="error-message">
                <img class="who-dat" src={whoDat} alt="whos-that-pokemon">
              </div>
            {/if}
            <div class="screen-content">
            {#if pokemonData}
              <div class="sprite-container">
                {#if isShiny}
                <img class="sprite-img" src="{shinySprite}" alt="{pokemonData.name}">
                {:else} 
                  <img class="sprite-img" src="{pokemonData.sprites.front_default}" alt="{pokemonData.name}">
                {/if}
                <button class="toggle-sprite" on:click={showShiny}>
                  {#if isShiny}
                    Normal
                  {:else}
                    Shiny
                  {/if}
                </button>
              </div>

              <div>
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
                    {#if pokemonTypeRelations[damageType].double_damage_to.length === 0}
                      None
                    {:else}
                      {#each pokemonTypeRelations[damageType].double_damage_to as superE}
                          {superE.name + " "}
                      {/each}
                    {/if}
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
        <div class="bottom-dex"><BottomDex /></div>
    </div> 
  </main>



  <style>

    h1, h2{
      margin-top: -30px;
    }
    main {
      color: black;
    }

    .kalos-dex {
      display: flex; 
      flex-direction: column;
      align-items: center;
      background-color: aquamarine;
      width: 440px;
      height: 350px;
      border-radius: 50px;
      border-left: 4px solid rgb(183, 183, 183);
      border-right: 4px solid rgb(183, 183, 183);
    }

    .opened {
      height: 650px;
    }

    .pokemon-search {
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: center;
      visibility: hidden;
    }

    .opened .pokemon-search {
      visibility: visible;
    }

    .screen {
      display: flex;
      flex-direction: column;
      justify-content: start;
      margin-top: 15%;
      height: 100%;
      width: 100%;
    }

    .opened .screen {
      margin:0px;
    }

    .search-btn {
      visibility: hidden;
      margin: 0 10px auto;
      width: 40px;
      height: 40px;
      border-radius: 100px;
      background-color: rgb(0, 153, 255);
    }

    .search-btn:hover {
      background-color: rgb(0, 153, 255, 0.5);
    }

    .opened .search-btn {
      visibility: visible;
    }

    input {
      display: block;
      font-size: large;
      color: rgb(124, 124, 124);
      width: 45%;
      padding: 10px;
      background-color: aquamarine;
      border: none;
      text-decoration-color: rgb(124, 124, 124);
      caret-color: transparent;
      text-align: center;
    }
    input::placeholder {
      text-align: center;
      text-decoration: underline;
    }

    input:focus {
      border: none;
      outline: none;
      text-align: center;
      caret-color: rgba(71, 71, 71, 0.342);
      color: rgb(71, 71, 71);
    }

    .screen-content {
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      align-items: center;
      margin: 0px auto;
      width: 100%;
    }

    .sprite-container {
      display: flex;
      align-items: center;
      justify-content: flex-end;
      width: 375px;
      height: 175px;
    }

    .toggle-sprite {
      margin-left: 18px;
      width: 90px;
      height: 30px;
      border-radius: 30px;
      font-size: 20px;
      background-color: rgb(0, 153, 255);
    }

    .toggle-sprite:hover {
      background-color: rgb(0, 153, 255, 0.5);
    }

    .sprite-img {
      width: 160px;
      height: 160px;
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
      padding: 5px;
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
      transition: transform 0.5s ease;
      margin-top: -20px;
    }

    .top-dex {
        transition: transform 0.5s ease;
        margin-top:-20px;
      }

    .opened .top-dex {
      transform: translateY(-100px);
    }

    .opened .bottom-dex {
      transform: translateY(100px);
      margin-top: -110px;
    }
 
    .start-btn {
      width: 130px;
      height: 130px;
      margin: -110px auto;
      border-radius: 100px;
      background-color: rgb(0, 238, 255);
      border: 12px solid rgb(0, 183, 207);
    }

    .opened .start-btn {
      visibility: hidden;
    }

    .start-btn:hover {
      background-color: rgba(0, 238, 255, 0.452);
      border: 12px solid rgb(0, 183, 207, 0.452);
    }

    .who-dat {
      height: 400px;
      width: 420px;
      border-radius: 20px;
      box-shadow: 2px 2px 15px 2px rgba(0, 0, 0, 0.5);
      margin: 5px;
    }
    
  </style>