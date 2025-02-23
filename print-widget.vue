<template>
  <div class="print-container bg-white p-8">
    <div class="print-header mb-8">
      <h1 class="text-2xl font-bold mb-6">Fiche Distributeur</h1>
      
      <!-- Contact Information -->
      <div class="contact-info space-y-3">
        <div class="field-group">
          <div class="label">Forme de politesse:</div>
          <div class="value">{{ record?.Forme_politesse }}</div>
        </div>
        <div class="field-group">
          <div class="label">Nom:</div>
          <div class="value">{{ record?.Nom_complet }}</div>
        </div>
        <div class="field-group">
          <div class="label">Adresse:</div>
          <div class="value">{{ record?.Adresse_complete }}</div>
        </div>
        <div class="field-group">
          <div class="label">Téléphone fixe:</div>
          <div class="value">{{ record?.Tel_fixe }}</div>
        </div>
        <div class="field-group">
          <div class="label">Téléphone portable:</div>
          <div class="value">{{ record?.Tel_portable }}</div>
        </div>
        <div class="field-group">
          <div class="label">Email:</div>
          <div class="value">{{ record?.Adresse_electronique }}</div>
        </div>
      </div>
    </div>

    <!-- Assigned Businesses Table -->
    <div class="businesses-section">
      <h2 class="text-xl font-bold mb-4">Commerces assignés</h2>
      <table class="w-full border-collapse">
        <thead>
          <tr class="bg-gray-100">
            <th class="border p-2 text-left">Nom du commerce</th>
            <th class="border p-2 text-left">Adresse complète</th>
            <th class="border p-2 text-left">Nombre de tirelires</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="commerce in commerces" :key="commerce.id" class="border-b">
            <td class="border p-2">{{ commerce.NOM }}</td>
            <td class="border p-2">{{ formatAddress(commerce) }}</td>
            <td class="border p-2">{{ commerce.Tirelires }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Print Button -->
    <button @click="printForm" class="mt-6 px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700 print:hidden">
      Imprimer
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const record = ref(null)
const commerces = ref([])

onMounted(() => {
  // Initialize Grist API
  grist.ready({ requiredAccess: 'read table' })
  
  // Subscribe to record changes
  grist.onRecord(rec => {
    record.value = rec
    // Fetch associated businesses
    if (rec?.id) {
      grist.docApi.fetchTable('T2024_commercants').then(table => {
        commerces.value = table.filter(t => 
          t.Distributeur && t.Distributeur.includes(rec.id)
        )
      })
    }
  })
})

const formatAddress = (commerce) => {
  return `${commerce.Rue} ${commerce.Numero}, ${commerce.Code_Postal} ${commerce.Commune}`
}

const printForm = () => {
  window.print()
}
</script>

<style>
.print-container {
  max-width: 210mm; /* A4 width */
  margin: 0 auto;
}

.field-group {
  display: grid;
  grid-template-columns: 200px 1fr;
  gap: 1rem;
}

.label {
  font-weight: 600;
}

/* Print styles */
@media print {
  .print-container {
    margin: 0;
    padding: 20mm;
  }

  @page {
    size: A4;
    margin: 0;
  }
  
  body {
    margin: 0;
  }
}
</style>