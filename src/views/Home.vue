<template>
  <div class="container mt-5">
    <h1 class="mb-4">Gestión de Usuarios</h1>

    <!-- Botón para abrir el formulario de creación -->
    <button class="btn btn-primary mb-3" @click="showAddForm = true">
      Agregar Usuario
    </button>

    <!-- Tabla de usuarios -->
    <UserTable
      :users="users"
      @edit-user="startEdit"
      @delete-user="confirmDelete"
    />

    <!-- Loader -->
    <div v-if="loading" class="text-center mt-4">
      <span>Cargando usuarios...</span>
    </div>

    <!-- Modal para formulario (crear/editar) -->
    <div v-if="showAddForm || userToEdit" class="modal-backdrop">
      <UserForm
        :user="userToEdit"
        @save="saveUser"
        @cancel="resetForm"
      />
    </div>

    <!-- Modal de confirmación para eliminar -->
    <div v-if="showDeleteModal" class="modal-backdrop">
      <div class="card p-4" style="min-width: 350px;">
        <h5>¿Eliminar usuario?</h5>
        <p>Esta acción no se puede deshacer.</p>
        <div class="d-flex justify-content-center gap-2">
          <button @click="deleteUserConfirmed" class="btn btn-danger">Eliminar</button>
          <button @click="cancelDelete" class="btn btn-secondary">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import UserTable from '../components/UserTable.vue'
import UserForm from '../components/UserForm.vue'

/* Estado principal */
const users = ref([])
const loading = ref(true)
const showAddForm = ref(false)
const userToEdit = ref(null)
const showDeleteModal = ref(false)
const userIdToDelete = ref(null)

/* Cargar usuarios desde la API (solo una vez al montar) */
onMounted(async () => {
  try {
    const res = await fetch('https://jsonplaceholder.typicode.com/users')
    const data = await res.json()
    users.value = data
  } catch (e) {
    console.error('Error al cargar usuarios:', e)
  } finally {
    loading.value = false
  }
})

/**
 * Guardar usuario (crear o editar)
 */
function saveUser(user) {
  if (user.id) {
    // Modo edición
    const index = users.value.findIndex(u => u.id === user.id)
    if (index !== -1) users.value[index] = user
  } else {
    // Modo creación
    const lastId = Math.max(...users.value.map(u => u.id))
    user.id = lastId + 1
    users.value.push(user)
  }
  resetForm()
}

/**
 * Activar modo edición
 */
function startEdit(user) {
  userToEdit.value = { ...user }
}

/**
 * Cerrar modal de formulario
 */
function resetForm() {
  showAddForm.value = false
  userToEdit.value = null
}

/**
 * Mostrar modal de confirmación para eliminar
 */
function confirmDelete(id) {
  userIdToDelete.value = id
  showDeleteModal.value = true
}

/**
 * Cancelar eliminación
 */
function cancelDelete() {
  showDeleteModal.value = false
  userIdToDelete.value = null
}

/**
 * Confirmar y eliminar usuario
 */
function deleteUserConfirmed() {
  users.value = users.value.filter(user => user.id !== userIdToDelete.value)
  cancelDelete()
}
</script>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}
.modal-backdrop .card {
  background-color: white;
  border-radius: 10px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  max-width: 400px;
  width: 100%;
  text-align: center;
}

.text-danger {
  color: red;
  font-size: 0.85rem;
}

</style>
