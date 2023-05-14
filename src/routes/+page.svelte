<style>
  .page {
    display: flex;
    flex-direction: column;
    align-items: center;

    padding: 30vh 1.5rem 0 1.5rem;
  }

  .piti-ali-ne-piti {
    margin: 0;
    font-size: 2rem;
    color: #eeedc4;
    text-align: center;
  }

  .btwn {
    width: 100%;
    color: #fef9f3;
    background-color: #f28e1c;
    font-size: 2rem;
    font-weight: bold;

    border: 0;
    margin: 0;
    padding: 1rem;
    border-radius: 6px;
  }

  .kontejnr {
    height: 2.75rem;
    margin: 1.5rem 0;
    overflow: hidden;
  }

  .animation-kontejnr {
    display: flex;
    flex-direction:column;
    align-items: center;
    gap: .25rem;

    animation: slideAnimation cubic-bezier(0.165, 0.84, 0.44, 1);
    animation-duration: 2000ms;
    animation-fill-mode: forwards;
  }

  .izraz {
    font-size: 2.5rem;
    font-weight: bold;
    line-height: 1;
    color: green;
    text-align: center;
  }

  @keyframes slideAnimation {
    0% {
      transform: translateY(-100%);
    }
    100% {
      transform: translateY(0%);
    }
  }
</style>

<script lang="ts">
	import { afterUpdate, onMount } from 'svelte';

  let ready: boolean = false;
  let izrazi: string[] = []
  let randomIzrazi: string[] = [];

  interface SheetsRow {
    c: Array<{v: string; f: string}>
  }

  function extractIzrazi(rawIzrazi: SheetsRow[]) {
    izrazi = rawIzrazi.map(i => i.c[1].v)
    ready = true;
  }

  async function fetchIzrazi() {
    const sheetUrl = 'https://docs.google.com/spreadsheets/d/10I_OywDPhd6fYf5AimUs-P0SYQwfOSy42QHHqDq2chs/gviz/tq?tqx=out:json&tq&gid=0';
    try {
      const response = await fetch(sheetUrl);
      const data = await response.text();
      const jsonContent = JSON.parse(data.substring(47).slice(0, -2));

      extractIzrazi(jsonContent.table.rows)
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  }

  function getRandomIzraz() {
    const randomIndex = Math.floor(Math.random() * izrazi.length);

    return izrazi[randomIndex];
  }

  function loadRandomIzrazi() {
    randomIzrazi = [];
    for (let i = 0; i < 10; i++) {
      const randomIzraz = getRandomIzraz();
      if (randomIzraz) randomIzrazi.push(randomIzraz);
    }

    // Reset the scroll animation by clearing and setting the 'animation-kontejnr' class
    const animationContainer = document.querySelector('.animation-kontejnr');
    console.log("kontejnr", animationContainer)
    if (animationContainer) {
      animationContainer.style.animation = 'none';
      animationContainer.offsetHeight; /* trigger reflow */
      animationContainer.style.animation = null; 
    }
  }

  onMount(async () => {
    await fetchIzrazi()
    loadRandomIzrazi();
  });
</script>

<div class="page">
  <h1 class="piti-ali-ne-piti">Napiti se ga? Kaj pa, če bi raje ponucal_a:</h1>
  <div class="kontejnr">
    <div class="animation-kontejnr">
      {#each randomIzrazi as string}
        <div class="izraz">{string}</div>
      {/each}
    </div>
  </div>
  <button class="btwn" disabled={!ready} on:click={loadRandomIzrazi}>Še eno rundo!</button>
</div>
