<template>
  <div class="container">
    <input type="text" v-model="searchQuery" placeholder="Rechercher un médicament..." class="search-input" />
    <Form @addMedicament="addMedicament" />
    <ul class="medicament-list">
      <ListItem v-for="medicament in filteredMedicaments" :key="medicament.id" :medicament="medicament"
        @deleteMedicament="deleteMedicament" @incrementQuantity="incrementQuantity"
        @decrementQuantity="decrementQuantity" @updateMedicament="updateMedicament" />
    </ul>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, computed } from "vue";
import Medicament from "../Medicament";
import Form from "./Form.vue";
import ListItem from "./ListItem.vue";

const medicaments = reactive([]);
const searchQuery = ref("");
const apiUrl = "https://apipharmacie.pecatte.fr/api/10/medicaments";

const fetchMedicaments = () => {
  fetch(apiUrl)
    .then((response) => response.json())
    .then((data) => {
      medicaments.splice(0, medicaments.length);
      data.forEach((item) =>
        medicaments.push(
          new Medicament(
            item.id,
            item.denomination,
            item.formepharmaceutique,
            item.qte,
            item.photo
          )
        )
      );
    })
    .catch((error) =>
      console.error("Erreur lors de la récupération des médicaments:", error)
    );
};

onMounted(() => {
  fetchMedicaments();
});

const filteredMedicaments = computed(() => {
  return medicaments.filter((medicament) =>
    medicament.denomination
      .toLowerCase()
      .includes(searchQuery.value.toLowerCase())
  );
});

const addMedicament = (name, form, quantity, photo = "") => {
  fetch(apiUrl, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      denomination: name,
      formepharmaceutique: form,
      qte: quantity,
      photo: photo,
    }),
  })
    .then((response) => response.json())
    .then((data) => {
      if (data.status === 1) {
        fetchMedicaments();
      } else {
        console.error("Erreur lors de l'ajout du médicament:", data);
      }
    })
    .catch((error) =>
      console.error("Erreur lors de l'ajout du médicament:", error)
    );
};

const deleteMedicament = (id) => {
  fetch(`${apiUrl}/${id}`, { method: "DELETE" })
    .then((response) => response.json())
    .then((data) => {
      if (data.status === 1) {
        fetchMedicaments();
      } else {
        console.error("Erreur lors de la suppression du médicament:", data);
      }
    })
    .catch((error) =>
      console.error("Erreur lors de la suppression du médicament:", error)
    );
};

const incrementQuantity = (id) => {
  const medicament = medicaments.find((m) => m.id === id);
  if (medicament) {
    medicament._qte += 1;
    updateMedicamentInAPI(medicament);
  }
};

const decrementQuantity = (id) => {
  const medicament = medicaments.find((m) => m.id === id);
  if (medicament && medicament._qte > 0) {
    medicament._qte -= 1;
    updateMedicamentInAPI(medicament);
  }
};

const updateMedicament = (updatedMedicament) => {
  fetch(`${apiUrl}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(updatedMedicament),
  })
    .then((response) => response.json())
    .then((data) => {
      if (data.status === 1) {
        fetchMedicaments();
      } else {
        console.error("Erreur lors de la mise à jour du médicament:", data);
      }
    })
    .catch((error) =>
      console.error("Erreur lors de la mise à jour du médicament:", error)
    );
};

const updateMedicamentInAPI = (medicament) => {
  fetch(apiUrl, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      id: medicament.id,
      denomination: medicament.denomination,
      formepharmaceutique: medicament.formepharmaceutique,
      qte: medicament.qte,
      photo: null,
    }),
  })
    .then((response) => response.json())
    .then((data) => {
      if (data.status === 1) {
        const index = medicaments.findIndex((m) => m.id === medicament.id);
        if (index !== -1) {
          medicaments[index] = new Medicament(
            medicament.id,
            medicament.denomination,
            medicament.formepharmaceutique,
            medicament.qte,
            medicament.photo
          );
        }
      } else {
        console.error("Erreur lors de la mise à jour:", data);
      }
    })
    .catch((error) => console.error("Erreur lors de la mise à jour:", error));
};
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.search-input {
  width: 250px;
  padding: 8px;
  margin-bottom: 20px;
  border: 2px solid #007bff;
  border-radius: 4px;
}

.search-input:focus {
  border-color: #0056b3;
  outline: none;
}

.medicament-list {
  list-style-type: none;
  padding: 0;
}
</style>
