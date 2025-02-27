<template>
  <li class="list-item">
    <div v-if="!isEditing" class="medicament-info">
      <span class="medicament-text">{{ medicament.pourAfficher() }}</span>
      <div class="button-group">
        <button @click="isEditing = true" class="edit-button">Modifier</button>
        <button @click="$emit('incrementQuantity', medicament.id)" class="action-button">+1</button>
        <button @click="$emit('decrementQuantity', medicament.id)" class="action-button">-1</button>
        <button @click="$emit('deleteMedicament', medicament.id)" class="action-button">Supprimer</button>
      </div>
    </div>
    <div v-else class="edit-form">
      <input type="text" v-model="newName" placeholder="Nom du médicament" class="input-field" />
      <input type="text" v-model="newForm" placeholder="Forme pharmaceutique" class="input-field" />
      <input type="file" @change="handleFileUpload" accept="image/*" class="input-field" />
      <button @click="saveChanges" class="save-button">Valider</button>
      <button @click="isEditing = false" class="cancel-button">Annuler</button>
    </div>
    <div v-if="medicament.photo">
      <img :src="`https://apipharmacie.pecatte.fr/images/${medicament.photo}`" alt="Photo du médicament"
        class="medicament-photo" />
    </div>
  </li>
</template>



<script setup>
import { ref, defineProps, defineEmits } from "vue";

const props = defineProps(["medicament"]);
const emit = defineEmits(["deleteMedicament", "updateMedicament", "incrementQuantity", "decrementQuantity"]);

const isEditing = ref(false);
const newName = ref(props.medicament.denomination);
const newForm = ref(props.medicament.formepharmaceutique);
const newPhoto = ref(null);
const hasNewPhoto = ref(false);

const handleFileUpload = event => {
  const file = event.target.files[0];
  if (file) {
    const validTypes = ["image/jpeg", "image/png", "image/jpg"];
    if (!validTypes.includes(file.type)) {
      alert("Veuillez télécharger une image au format .jpg, .jpeg ou .png");
      return;
    }
    const reader = new FileReader();
    reader.onload = () => {
      newPhoto.value = reader.result.split(",")[1];
      hasNewPhoto.value = true;
    };
    reader.readAsDataURL(file);
  }
};

const saveChanges = () => {
  const updatedMedicament = {
    id: props.medicament.id,
    denomination: newName.value,
    formepharmaceutique: newForm.value,
    photo: hasNewPhoto.value ? newPhoto.value : null
  };
  emit("updateMedicament", updatedMedicament);
  isEditing.value = false;
  hasNewPhoto.value = false;
};
</script>



<style scoped>
.list-item {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin: 5px 0;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: auto;
}

.medicament-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  gap: 10px;
}

.medicament-text {
  color: rgb(15, 15, 15);
  flex: 1;
  width: 100%;
  text-align: left;
}

.button-group {
  display: flex;
  gap: 5px;
}

.edit-button,
.save-button,
.cancel-button,
.action-button {
  padding: 5px 10px;
  background-color: #007BFF;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
}

.edit-button:hover,
.save-button:hover,
.cancel-button:hover,
.action-button:hover {
  background-color: #0056b3;
}

.input-field {
  margin: 0 5px;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 12px;
}

.input-field[type="file"] {
  color: black !important;
}

.input-field[type="file"]::file-selector-button {
  color: black !important;
  background-color: white;
  border: 1px solid #ccc;
  padding: 5px;
  border-radius: 4px;
  cursor: pointer;
}

.input-field[type="file"]::file-selector-button:hover {
  background-color: #f0f0f0;
}

.medicament-photo {
  max-width: 100px;
  height: auto;
  margin-top: 10px;
}
</style>
