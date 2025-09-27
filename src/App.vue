<script setup>
import { ref, onMounted } from 'vue'

const pokemons = ref([])
const selected = ref(null) // almacena el Pokémon seleccionado

onMounted(async () => {
  const res = await fetch('https://pokeapi.co/api/v2/pokemon?limit=12')
  const data = await res.json()

  const results = await Promise.all(
    data.results.map(async (p) => {
      const pokeRes = await fetch(p.url)
      const pokeData = await pokeRes.json()
      return {
        id: pokeData.id,
        name: pokeData.name,
        image: pokeData.sprites.other['official-artwork'].front_default,
        types: pokeData.types.map((t) => t.type.name),
        base_experience: pokeData.base_experience
      }
    })
  )

  pokemons.value = results
})

function showDetails(poke) {
  selected.value = poke
}
</script>

<template>
  <h1 class="title">Pokédex</h1>
  
  <!-- lista de Pokémon -->
  <div class="grid">
    <div 
      v-for="poke in pokemons" 
      :key="poke.id" 
      class="card" 
      @click="showDetails(poke)"
    >
      <img :src="poke.image" :alt="poke.name" />
      <h2>{{ poke.name }}</h2>
    </div>
  </div>

  <!-- detalle del Pokémon seleccionado (modal) -->
  <div v-if="selected" class="overlay" @click.self="selected = null">
    <div class="details">
      <h2>{{ selected.name }}</h2>
      <img :src="selected.image" :alt="selected.name" />
      <p><strong>ID:</strong> #{{ selected.id }}</p>
      
      <div class="types">
        <span v-for="t in selected.types" :key="t" :class="['type', t]">
          {{ t }}
        </span>
      </div>

      <p><strong>Experiencia base:</strong> {{ selected.base_experience }}</p>
      <button @click="selected = null">Cerrar</button>
    </div>
  </div>
</template>

<style>
/* Fondo del modal */
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.85); /* ✅ más oscuro */
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Tarjeta de detalles */
.details {
  background: linear-gradient(145deg, #e63946, #f1faee); /* ✅ estilo Pokédex */
  padding: 25px;
  border-radius: 20px;
  max-width: 400px;
  text-align: center;
  box-shadow: 0 8px 25px rgba(0,0,0,0.4);
  animation: pop 0.3s ease;
  color: #222;
}
.details img {
  width: 170px;
  height: 170px;
}
.details h2 {
  text-transform: capitalize;
  margin-bottom: 10px;
  font-size: 1.8rem;
  color: #1d3557;
}

/* Chips de tipos */
.types {
  margin: 15px 0;
}
.type {
  display: inline-block;
  padding: 6px 12px;
  margin: 5px;
  border-radius: 25px;
  color: #fff;
  font-weight: bold;
  text-transform: capitalize;
  font-size: 0.9rem;
  box-shadow: 0 2px 6px rgba(0,0,0,0.2);
}

/* Colores por tipo */
.type.grass { background: #4caf50; }
.type.poison { background: #9c27b0; }
.type.fire { background: #e53935; }
.type.water { background: #1e88e5; }
.type.electric { background: #ffeb3b; color: #000; }
.type.bug { background: #8bc34a; }
.type.ghost { background: #673ab7; }
.type.psychic { background: #e91e63; }
.type.normal { background: #9e9e9e; }
.type.fighting { background: #ff5722; }
.type.rock { background: #795548; }
.type.ground { background: #a1887f; }
.type.fairy { background: #f48fb1; }
.type.dragon { background: #3f51b5; }
.type.ice { background: #00bcd4; }

/* Botón cerrar */
.details button {
  margin-top: 15px;
  padding: 10px 20px;
  background: #1d3557;
  color: #fff;
  border: none;
  border-radius: 12px;
  font-size: 1rem;
  cursor: pointer;
  transition: background 0.2s;
}
.details button:hover {
  background: #457b9d;
}

.card h2 {
  margin-top: 10px;
  font-size: 1.1rem;
  font-weight: bold;
  color: #1a73e8; /* ✅ azul tipo link */
  text-transform: capitalize;
  cursor: pointer;
  text-decoration: underline; /* opcional si quieres estilo link */
  transition: color 0.2s;
}
.card h2:hover {
  color: #0b57d0; /* azul más oscuro al pasar el mouse */
}



</style>

