<template>
  <div class="controls">
    <select v-model="sortKey" class="select-input" @change="emitFilters">
      <option value="name">Sort by Name</option>
      <option value="age">Sort by Age</option>
      <option value="status">Sort by Status</option>
    </select>
    
    <select v-model="statusFilter" class="select-input" @change="emitFilters">
      <option value="all">All Status</option>
      <option value="active">Active</option>
      <option value="inactive">Inactive</option>
    </select>
    
    <button @click="$emit('add')" class="btn btn-primary">
      Add User
    </button>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  name: 'FilterControl',
  emits: ['filter', 'add'],
  setup(props, { emit }) {
    const sortKey = ref('name')
    const statusFilter = ref('all')

    const emitFilters = () => {
      emit('filter', {
        sortKey: sortKey.value,
        statusFilter: statusFilter.value
      })
    }

    return {
      sortKey,
      statusFilter,
      emitFilters
    }
  }
}
</script>

<style scoped>
.controls {
  display: flex;
  gap: 1rem;
  align-items: center;
  margin-bottom: 1rem;
}

.select-input {
  padding: 0.5rem;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  color: #333;
  background-color: #f9f9f9;
  transition: border-color 0.3s, background-color 0.3s;
}

.select-input:focus {
  border-color: #007bff;
  outline: none;
}

.btn-primary {
  padding: 0.5rem 1rem;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn-primary:hover {
  background-color: #0056b3;
}

.btn-primary:focus {
  outline: none;
}
</style>
