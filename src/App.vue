<script setup>
import { ref, computed } from 'vue';

// Reaktivna stanja
const noviNaziv = ref('');
const novaCijena = ref(0);
const kosarica = ref([]);

// Funkcije (metode)
const dodajUKosaricu = () => {
  // Osiguranje da cijena nije ispod 0
  //const cijenaZaDodati = novaCijena.value < 0 ? 0 : novaCijena.value;
  let cijenaZaDodati;

if (novaCijena.value < 0) {
    cijenaZaDodati = 0;
} else {
    cijenaZaDodati = novaCijena.value;
}

  // Provjera postoji li već artikl s istim imenom
  const postojeciArtikl = kosarica.value.find(
    (artikl) => artikl.naziv.toLowerCase() === noviNaziv.value.toLowerCase()
  );

  if (postojeciArtikl) {
    postojeciArtikl.kolicina++;
  } else {
    kosarica.value.push({
      naziv: noviNaziv.value,
      cijena: cijenaZaDodati,
      kolicina: 1
    });
  }

  // Resetiranje polja nakon unosa
  noviNaziv.value = '';
  novaCijena.value = 0;
};

const promijeniKolicinu = (index, promjena) => {
  const artikl = kosarica.value[index];
  artikl.kolicina += promjena;

  // Količina ne smije ići ispod 1 (prema zadatku, za micanje koristimo "Ukloni")
  if (artikl.kolicina < 1) {
    artikl.kolicina = 1;
  }
};

const ukloniIzKosarice = (index) => {
  // splice(redni_broj, koliko_elemenata)
  kosarica.value.splice(index, 1);
};

// Computed property za ukupnu cijenu (automatski se ažurira)
const ukupniTrosak = computed(() => {
  return kosarica.value.reduce((suma, artikl) => {
    return suma + (artikl.cijena * artikl.kolicina);
  }, 0).toFixed(2);
});
</script>

<template>
  <div class="container">
    <h1>VueShop</h1>

    <div class="input-section">
      <input 
        v-model="noviNaziv" 
        type="text" 
        placeholder="Naziv proizvoda"
      >
      <input 
        v-model.number="novaCijena" 
        type="number" 
        placeholder="Cijena"
      >
      
      <button 
        @click="dodajUKosaricu" 
        :disabled="!noviNaziv"
      >
        Dodaj artikl
      </button>
    </div>

    <hr>

    <h2>Vaša košarica</h2>

    <p v-if="kosarica.length === 0">Košarica je prazna!</p>

    <ul v-else>
      <li v-for="(artikl, index) in kosarica" :key="index">
        <div class="artikl-info">
          <strong>{{ artikl.naziv }}</strong>
          <span>{{ artikl.cijena }} € / kom</span>
        </div>

        <div class="kontrole">
          <button @click="promijeniKolicinu(index, -1)">-</button>
          <span class="kolicina">{{ artikl.kolicina }}</span>
          <button @click="promijeniKolicinu(index, 1)">+</button>
          
          <span class="suma-artikla">
            Ukupno: {{ (artikl.cijena * artikl.kolicina).toFixed(2) }} €
          </span>

          <button @click="ukloniIzKosarice(index)" class="btn-ukloni">Ukloni</button>
        </div>
      </li>
    </ul>

    <div v-if="kosarica.length > 0" class="total">
      <h3>Ukupno za platiti: {{ ukupniTrosak }} €</h3>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 40px auto;
  font-family: Arial, sans-serif;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

input {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 8px 15px;
  cursor: pointer;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 0;
  border-bottom: 1px solid #eee;
}

.kontrole {
  display: flex;
  align-items: center;
  gap: 10px;
}

.kolicina {
  font-weight: bold;
  min-width: 20px;
  text-align: center;
}

.btn-ukloni {
  background-color: #ff4d4d;
  font-size: 0.8em;
}

.total {
  margin-top: 20px;
  text-align: right;
  color: #2c3e50;
}
</style>