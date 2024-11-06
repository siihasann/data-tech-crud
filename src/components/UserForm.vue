<template>
    <div class="fixed inset-0 flex items-center justify-center bg-gray-800 bg-opacity-50 z-50">
      <div class="bg-white p-6 rounded-lg shadow-lg max-w-md w-full">
        <h2 class="text-xl font-bold mb-4">{{ isEditing ? "Edit User" : "Add User" }}</h2>
        <form @submit.prevent="handleSubmit">
          <div class="mb-4">
            <label class="block text-sm font-medium mb-1">Name:</label>
            <input v-model="formData.name" type="text" :class="{ 'border-red-500': errors.name }" class="w-full px-3 py-2 border rounded"/>
            <span v-if="errors.name" class="text-red-500 text-xs">{{ errors.name }}</span>
          </div>
          <div class="mb-4">
            <label class="block text-sm font-medium mb-1">Email:</label>
            <input v-model="formData.email" type="email" :class="{ 'border-red-500': errors.email }" class="w-full px-3 py-2 border rounded"/>
            <span v-if="errors.email" class="text-red-500 text-xs">{{ errors.email }}</span>
          </div>
          <div class="mb-4">
            <label class="block text-sm font-medium mb-1">Age:</label>
            <input v-model="formData.age" type="number" :class="{ 'border-red-500': errors.age }" class="w-full px-3 py-2 border rounded"/>
            <span v-if="errors.age" class="text-red-500 text-xs">{{ errors.age }}</span>
          </div>
          <div class="mb-4">
            <label class="block text-sm font-medium mb-1">Status:</label>
            <select v-model="formData.status" class="w-full px-3 py-2 border rounded">
              <option value="active">Active</option>
              <option value="inactive">Inactive</option>
            </select>
          </div>
          <div class="flex justify-end space-x-2">
            <button type="button" @click="$emit('close')" class="px-4 py-2 text-gray-600 bg-gray-200 rounded hover:bg-gray-300">Cancel</button>
            <button type="submit" class="px-4 py-2 text-white bg-blue-600 rounded hover:bg-blue-700">{{ isEditing ? "Save Changes" : "Add User" }}</button>
          </div>
        </form>
      </div>
    </div>
  </template>
  
  <script>
  import { reactive, computed } from "vue";
  
  export default {
    name: "UserForm",
    props: {
      user: {
        type: Object,
        default: null,
      },
    },
    emits: ["submit", "close"],
    setup(props, { emit }) {
      const formData = reactive({
        name: props.user?.name || "",
        email: props.user?.email || "",
        age: props.user?.age || "",
        status: props.user?.status || "active",
      });
  
      const errors = reactive({});
      const isEditing = computed(() => !!props.user);
  
      const handleSubmit = () => {
        errors.name = !formData.name ? "Name is required" : "";
        errors.email = !formData.email ? "Email is required" : "";
        errors.age = !formData.age ? "Age is required" : "";
        if (!errors.name && !errors.email && !errors.age) {
          emit("submit", { ...formData });
        }
      };
  
      return {
        formData,
        errors,
        isEditing,
        handleSubmit,
      };
    },
  };
  </script>
  