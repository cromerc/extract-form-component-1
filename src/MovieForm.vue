<script setup>
import { reactive } from 'vue';

const props = defineProps(['modelValue']);
const emit = defineEmits(['update:modelValue', 'cancel']);

const genres = reactive([
  { text: "Drama", value: "Drama" },
  { text: "Crime", value: "Crime" },
  { text: "Action", value: "Action" },
  { text: "Comedy", value: "Comedy" },
]);

const validations = reactive({
  name: "required",
  genres: "required",
});

const validationRules = (rule) => {
  if (rule === "required") return /^ *$/;

  return null;
};

function validate() {
  let valid = true;
  clearErrors();
  for (const [field, rule] of Object.entries(validations)) {
    const validation = validationRules(rule);
    if (validation) {
      if (validation.test(props.modelValue[field] || "")) {
        errors[field] = `${field} is ${rule}`;
        valid = false;
      }
    }
  }

  return valid;
}

function saveMovie() {
  if (props.modelValue.id) {
    updateMovie();
  } else {
    addMovie();
  }
}

function updateMovie() {
  if (validate()) {
    emit('update:modelValue', props.modelValue);
    cleanUpForm();
  }
}

function addMovie() {
  if (validate()) {
    emit('update:modelValue', props.modelValue);
    cleanUpForm();
  }
}

const errors = reactive({
  name: null,
  description: null,
  image: null,
  inTheaters: null,
  genres: null,
});

function clearErrors() {
  errors.name = null;
  errors.description = null;
  errors.image = null;
  errors.genres = null;
  errors.inTheaters = null;
}

function cleanUpForm() {
  props.modelValue.id = null;
  props.modelValue.name = null;
  props.modelValue.description = null;
  props.modelValue.image = null;
  props.modelValue.genres = null;
  props.modelValue.inTheaters = false;
  clearErrors();
}

function cancel() {
  cleanUpForm();
  emit('cancel');
}

</script>

<template>
  <div class="modal-wrapper">
    <div class="modal-wrapper-inner">
      <form @submit.prevent="saveMovie">
        <input type="hidden" name="id" v-model="modelValue.id" />
        <div class="movie-form-input-wrapper">
          <label for="name">Name</label>
          <input type="text" name="name" v-model="modelValue.name" class="movie-form-input" />
          <span class="movie-form-error">{{ errors.name }}</span>
        </div>
        <div class="movie-form-input-wrapper">
          <label for="description">Description</label>
          <textarea type="text" name="description" v-model="modelValue.description" class="movie-form-textarea" />
          <span class="movie-form-error">{{ errors.description }}</span>
        </div>
        <div class="movie-form-input-wrapper">
          <label for="image">Image</label>
          <input type="text" name="image" v-model="modelValue.image" class="movie-form-input" />
          <span class="movie-form-error">{{ errors.image }}</span>
        </div>
        <div class="movie-form-input-wrapper">
          <label for="genre">Genres</label>
          <select name="genre" v-model="modelValue.genres" class="movie-form-input" multiple>
            <option v-for="option in genres" :key="option.value" :value="option.value">
              {{ option.text }}
            </option>
          </select>
          <span class="movie-form-error">
            {{ errors.genres }}
          </span>
        </div>
        <div class="movie-form-input-wrapper">
          <label for="genre" class="movie-form-checkbox-label">
            <input type="checkbox" v-model="modelValue.inTheaters" :true-value="true" :false-value="false"
              class="movie-form-checkbox" />
            <span>In theaters</span>
          </label>
          <span class="movie-form-error">
            {{ errors.inTheaters }}
          </span>
        </div>
        <div class="movie-form-actions-wrapper">
          <button type="button" class="button" @click="cancel">
            Cancel
          </button>

          <button type="submit" class="button-primary">
            <span v-if="modelValue.id">Update</span>
            <span v-else>Create</span>
          </button>
        </div>
      </form>
    </div>
  </div>
</template>
