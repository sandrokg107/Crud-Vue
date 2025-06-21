<template>
  <table class="table table-striped ">
    <thead class="table-dark">
      <tr>
        <th>Nombre</th>
        <th>Username</th>
        <th>Email</th>
        <th>Teléfono</th>
        <th>Acciones</th>
      </tr>
    </thead>

    <tbody>
      <!-- Iterar sobre los usuarios y mostrar cada uno -->
      <tr v-for="user in users" :key="user.id">
        <td>{{ user.name }}</td>
        <td>{{ user.username }}</td>
        <td>{{ user.email }}</td>
        <td>{{ user.phone }}</td>
        <td>
          <!-- Botón para editar usuario -->
          <button
            class="btn btn-sm btn-primary me-2"
            @click="$emit('edit-user', user)"
          >
            Editar
          </button>

          <!-- Botón para eliminar usuario -->
          <button
            class="btn btn-sm btn-danger"
            @click="$emit('delete-user', user.id)"
          >
            Eliminar
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script setup>
// Props: Lista de usuarios a mostrar
const props = defineProps({
  users: {
    type: Array,
    required: true
  }
})

// Eventos emitidos: editar o eliminar usuario
const emit = defineEmits(['edit-user', 'delete-user'])
</script>


// C:\Users\sandr\myper-crud