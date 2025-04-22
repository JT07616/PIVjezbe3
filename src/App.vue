<script setup>
import { ref, computed } from 'vue';

const noviProizvod = ref({
  naziv: '',
  cijena: 1
});

const kosarica = ref([]);

const mozeDodati = computed(() => {
  return noviProizvod.value.naziv.trim() !== '' && noviProizvod.value.cijena >= 0.01;
});

const ukupno = computed(() => {
  return kosarica.value.reduce((suma, p) => suma + p.kolicina * p.cijena, 0);
});

function dodajProizvod() {
  const naziv = noviProizvod.value.naziv.trim();
  const cijena = parseFloat(noviProizvod.value.cijena);

  if (naziv === '' || cijena < 0.01) return;

  const postoji = kosarica.value.find(p => p.naziv.toLowerCase() === naziv.toLowerCase());
  if (postoji) {
    postoji.kolicina++;
  } else {
    kosarica.value.push({ naziv, cijena, kolicina: 1 });
  }

  noviProizvod.value.naziv = '';
  noviProizvod.value.cijena = 1;
}

function promijeniKolicinu(index, kolicinskaPromjena) {
  const proizvod = kosarica.value[index];
  proizvod.kolicina += kolicinskaPromjena;

  if (proizvod.kolicina <= 0) {
    ukloni(index);
  }
}

function ukloni(index) {
  kosarica.value.splice(index, 1);
}
</script>

<template>
  <div class="max-w-3xl mx-auto mt-8 space-y-6 px-4">
    
    <div class="flex gap-2">
      <input v-model="noviProizvod.naziv" type="text" class="border p-2 rounded w-1/2" placeholder="Naziv proizvoda"/>
      <input v-model.number="noviProizvod.cijena" type="number" min="0.01"class="border p-2 rounded w-1/4" />
      <button @click="dodajProizvod" :disabled="!mozeDodati" class="bg-blue-500 text-white px-4 py-2 rounded disabled:opacity-50">Dodaj</button>
    </div>

    <div v-if="kosarica.length === 0" class="text-gray-500 italic">
      Košarica je prazna!
    </div>

    <div v-else class="space-y-4">
      <div v-for="(p, i) in kosarica" :key="i" class="flex flex-col sm:flex-row sm:items-center justify-between p-4 border rounded shadow-sm gap-2 sm:gap-0">
       
        <div class="flex-1">
          <div class="font-semibold">{{ p.naziv }}</div>
          <div class="text-sm text-gray-600">{{ p.cijena.toFixed(2) }} € / kom</div>
        </div>

       <div class="flex items-center gap-2">
          <button @click="promijeniKolicinu(i, -1)" class="px-2 py-1 bg-gray-200 rounded disabled:opacity-50" :disabled="p.kolicina <= 1">-</button>
          <span>{{ p.kolicina }}</span>
          <button @click="promijeniKolicinu(i, 1)" class="px-2 py-1 bg-gray-200 rounded">+</button>
        </div>

        <div class="w-24 text-right font-medium">
          {{ (p.kolicina * p.cijena).toFixed(2) }} €
        </div>

      
        <button @click="ukloni(i)" class="text-red-500 text-sm hover:underline">
          Ukloni
        </button>
      
      </div>

    </div>

   <div v-if="kosarica.length > 0" class="text-right font-semibold text-lg">
      Ukupno: {{ ukupno.toFixed(2) }} €
    </div>

  </div>
  
</template>

<style scoped>
</style>