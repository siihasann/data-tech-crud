<template>
  <div class="overflow-x-auto">
    <table class="table-auto w-full border-collapse bg-white shadow-lg rounded-lg mb-4">
      <thead>
        <tr class="bg-blue-600 text-white uppercase text-sm leading-normal">
          <th class="py-4 px-6 text-left">Name</th>
          <th class="py-4 px-6 text-left">Email</th>
          <th class="py-4 px-6 text-left">Age</th>
          <th class="py-4 px-6 text-left">Status</th>
          <th class="py-4 px-6 text-center">Actions</th>
        </tr>
      </thead>
      <tbody class="text-gray-700 text-sm font-light">
        <tr 
          v-for="user in paginatedUsers" 
          :key="user.id" 
          class="border-b border-gray-200 bg-white hover:bg-gray-50 transition-all duration-200 ease-in-out"
        >
          <td class="py-4 px-6 text-left">{{ user.name }}</td>
          <td class="py-4 px-6 text-left">{{ user.email }}</td>
          <td class="py-4 px-6 text-left">{{ user.age }}</td>
          <td class="py-4 px-6 text-left">
            <span 
              :class="user.status === 'active' ? 'bg-green-100 text-green-600' : 'bg-red-100 text-red-600'" 
              class="py-1 px-3 rounded-full text-xs font-semibold"
            >
              {{ user.status }}
            </span>
          </td>
          <td class="py-4 px-6 text-center flex justify-center space-x-2">
            <button 
              @click="$emit('edit', user)" 
              class="bg-yellow-400 text-white py-1 px-4 rounded-full hover:bg-yellow-500 focus:outline-none focus:ring-2 focus:ring-yellow-300 transition-all"
            >
              Edit
            </button>
            <button 
              @click="$emit('delete', user.id)" 
              class="bg-red-500 text-white py-1 px-4 rounded-full hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-300 transition-all"
            >
              Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Pagination Controls -->
    <div class="flex justify-center items-center space-x-4">
      <button
        @click="prevPage"
        :disabled="currentPage === 1"
        class="bg-blue-500 text-white px-4 py-2 rounded disabled:bg-gray-300"
      >
        Prev
      </button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button
        @click="nextPage"
        :disabled="currentPage === totalPages"
        class="bg-blue-500 text-white px-4 py-2 rounded disabled:bg-gray-300"
      >
        Next
      </button>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: "UserTable",
  props: {
    users: {
      type: Array,
      required: true,
    },
  },
  setup(props) {
    // Pagination state
    const currentPage = ref(1)
    const pageSize = 10  // Number of users per page

    // Calculate total pages
    const totalPages = computed(() => Math.ceil(props.users.length / pageSize))

    // Get paginated users for the current page
    const paginatedUsers = computed(() => {
      const start = (currentPage.value - 1) * pageSize
      const end = start + pageSize
      return props.users.slice(start, end)
    })

    // Pagination controls
    const nextPage = () => {
      if (currentPage.value < totalPages.value) {
        currentPage.value++
      }
    }

    const prevPage = () => {
      if (currentPage.value > 1) {
        currentPage.value--
      }
    }

    return {
      paginatedUsers,
      currentPage,
      totalPages,
      nextPage,
      prevPage
    }
  }
}
</script>

<style scoped>
.overflow-x-auto {
  overflow-x: auto;
  padding: 1rem;
}

.table-auto {
  min-width: 600px;
}

.pagination-btn {
  background-color: #3b82f6;
  color: #ffffff;
  padding: 8px 16px;
  border-radius: 8px;
}

.pagination-btn:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}
</style>
