<!-- src/components/UserForm.vue -->
<template>
  <div class="card p-4" style="min-width: 400px;">
    <!-- Título dinámico según si es edición o creación -->
    <h5>{{ isEditMode ? 'Editar Usuario' : 'Nuevo Usuario' }}</h5>

    <form @submit.prevent="handleSubmitUserForm">
      <!-- Campo: Nombre -->
      <div class="mb-2">
        <label for="name">Nombre:</label>
        <input
          id="name"
          v-model="localUser.name"
          class="form-control"
          required
        />
      </div>

      <!-- Campo: Username -->
      <div class="mb-2">
        <label for="username">Username:</label>
        <input
          id="username"
          v-model="localUser.username"
          class="form-control"
          required
        />
      </div>

      <!-- Campo: Email -->
      <div class="mb-2">
        <label for="email">Email:</label>
        <input
          id="email"
          v-model="localUser.email"
          type="email"
          class="form-control"
          required
        />
      </div>

      <!-- Campo: Teléfono -->
      <div class="mb-3">
        <label for="phone">Teléfono:</label>
        <input
          id="phone"
          v-model="localUser.phone"
          class="form-control"
          required
        />
      </div>

      <!-- Botones de acción -->
      <div class="d-flex justify-content-end gap-2">
        <button type="submit" class="btn btn-success">Guardar</button>
        <button type="button" @click="$emit('cancel')" class="btn btn-secondary">
          Cancelar
        </button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref, watch, computed } from 'vue'

// Props: usuario a editar (si existe)
const props = defineProps({
  user: Object
})

// Emits: acciones hacia el componente padre
const emit = defineEmits(['save', 'cancel'])

// Estado local del formulario
const localUser = ref({
  name: '',
  username: '',
  email: '',
  phone: ''
})

// Computed: saber si estamos editando
const isEditMode = computed(() => !!props.user?.id)

// Sincroniza los datos del usuario recibido en props (para edición)
watch(
  () => props.user,
  (newUser) => {
    if (newUser) {
      localUser.value = { ...newUser }
    } else {
      localUser.value = { name: '', username: '', email: '', phone: '' }
    }
  },
  { immediate: true }
)

// Emitir usuario guardado al padre
function handleSubmitUserForm() {
  emit('save', localUser.value)
}
</script>
