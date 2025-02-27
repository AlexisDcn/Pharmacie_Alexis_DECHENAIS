<template>
  <form @submit.prevent="addMedicament" class="form-style">
    <input type="text" v-model="medicamentName" placeholder="Nom du médicament" required class="input-style" />
    <input type="text" v-model="pharmaceuticalForm" placeholder="Forme pharmaceutique" required class="input-style" />
    <input type="number" v-model="quantity" placeholder="Quantité" required class="input-style" />
    <input type="file" @change="handleFileUpload" accept="image/*" class="input-style file-input" />
    <input type="submit" value="Ajouter" class="button-style" />
  </form>
</template>



<script setup>
import { ref } from "vue";

const medicamentName = ref("");
const pharmaceuticalForm = ref("");
const quantity = ref("");
const photo = ref(null);

const emit = defineEmits(["addMedicament"]);

const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    const validTypes = ["image/jpeg", "image/png", "image/jpg"];
    if (!validTypes.includes(file.type)) {
      alert("Veuillez télécharger une image au format .jpg, .jpeg ou .png");
      return;
    }
    const reader = new FileReader();
    reader.onload = () => {
      photo.value = reader.result.split(",")[1];
    };
    reader.readAsDataURL(file);
  }
};

const addMedicament = () => {
  emit("addMedicament", medicamentName.value, pharmaceuticalForm.value, quantity.value, photo.value);
};
</script>



<style scoped>
.form-style {
  display: flex;
  flex-direction: column;
  max-width: 300px;
}

.input-style {
  margin: 10px 0;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  color: black;
}

.file-input {
  color: black;
}

.button-style {
  padding: 10px;
  background-color: #007BFF;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.button-style:hover {
  background-color: #0056b3;
}
</style>
