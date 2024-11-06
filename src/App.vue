<template>
  <div class="container mx-auto p-6">
    <h1 class="text-2xl font-bold text-center mb-6">DataTech Dashboard</h1>

    <!-- Loading & Error States -->
    <div v-if="loading" class="text-center text-blue-500 mb-4">Loading users data...</div>
    <div v-if="error" class="text-center text-red-500 mb-4">{{ error }}</div>

    <FilterControl @filter="handleFilters" @add="showAddForm = true" />

    <UserTable
      :users="filteredAndSortedUsers"
      @edit="editUser"
      @delete="deleteUser"
    />

    <UserForm
      v-if="showAddForm || editingUser"
      :user="editingUser"
      @submit="handleSubmit"
      @close="closeForm"
    />
  </div>
</template>

<script>
import { ref, computed, onMounted } from "vue";
import UserTable from "./components/UserTable.vue";
import UserForm from "./components/UserForm.vue";
import FilterControl from "./components/FilterControl.vue";

export default {
  name: "App",
  components: {
    UserTable,
    UserForm,
    FilterControl,
  },
  setup() {
    const users = ref([]);
    const loading = ref(false);
    const error = ref(null);
    const showAddForm = ref(false);
    const editingUser = ref(null);
    const filters = ref({
      sortKey: "name",
      statusFilter: "all",
    });

    const loadUsers = async () => {
      loading.value = true;
      error.value = null;
      try {
        const response = await fetch("https://api.github.com/users");
        const data = await response.json();
        users.value = data.map((user) => ({
          id: user.id,
          name: user.login,
          email: `${user.login}@example.com`,
          age: Math.floor(Math.random() * 30) + 20,
          status: Math.random() > 0.5 ? "active" : "inactive",
        }));
        localStorage.setItem("users", JSON.stringify(users.value));
      } catch (err) {
        error.value = "Failed to load users data";
        const savedUsers = localStorage.getItem("users");
        if (savedUsers) {
          users.value = JSON.parse(savedUsers);
          error.value = "Loaded from cached data";
        }
      } finally {
        loading.value = false;
      }
    };

    const filteredAndSortedUsers = computed(() => {
      let filtered = [...users.value];
      if (filters.value.statusFilter !== "all") {
        filtered = filtered.filter(
          (user) => user.status === filters.value.statusFilter
        );
      }
      filtered.sort((a, b) => {
        if (filters.value.sortKey === "age") {
          return a.age - b.age;
        }
        return a[filters.value.sortKey].localeCompare(b[filters.value.sortKey]);
      });
      return filtered;
    });

    const handleFilters = (newFilters) => {
      filters.value = newFilters;
    };

    const handleSubmit = (userData) => {
      if (editingUser.value) {
        const index = users.value.findIndex(
          (u) => u.id === editingUser.value.id
        );
        users.value[index] = { ...users.value[index], ...userData };
      } else {
        users.value.push({
          id: Date.now(),
          ...userData,
        });
      }
      localStorage.setItem("users", JSON.stringify(users.value));
      closeForm();
    };

    const editUser = (user) => {
      editingUser.value = user;
      showAddForm.value = true;
    };

    const deleteUser = (userId) => {
      if (confirm("Are you sure you want to delete this user?")) {
        users.value = users.value.filter((u) => u.id !== userId);
        localStorage.setItem("users", JSON.stringify(users.value));
      }
    };

    const closeForm = () => {
      showAddForm.value = false;
      editingUser.value = null;
    };

    onMounted(() => {
      const savedUsers = localStorage.getItem("users");
      if (savedUsers) {
        users.value = JSON.parse(savedUsers);
      } else {

        loadUsers();

      }
      
    });

    return {
      users,
      loading,
      error,
      showAddForm,
      editingUser,
      filteredAndSortedUsers,
      handleSubmit,
      handleFilters,
      editUser,
      deleteUser,
      closeForm,
    };
  },
};
</script>
