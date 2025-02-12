<script setup>
import { ref } from "vue";
import Swal from "sweetalert2";

const isOpen = ref(false);
const user = ref({ name: "", email: "" });
const errors = ref({ name: "", email: "" });

const openModal = () => {
  isOpen.value = true;
};

const closeModal = () => {
  isOpen.value = false;
  user.value = { name: "", email: "" }; // Limpiar formulario al cerrar
  errors.value = { name: "", email: "" };
};

const createUser = async () => {
  errors.value = { name: "", email: "" };

  if (!user.value.name) errors.value.name = "El nombre es obligatorio";
  if (!user.value.email) errors.value.email = "El correo es obligatorio";
  if (!user.value.email.includes("@")) errors.value.email = "Correo no válido";

  if (errors.value.name || errors.value.email) return;

  try {
    const res = await fetch("https://jsonplaceholder.typicode.com/users", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(user.value),
    });

    if (!res.ok) throw new Error("Error al crear usuario");

    Swal.fire({
      icon: "success",
      title: "Usuario creado",
      text: `El usuario ${user.value.name} ha sido registrado`,
      timer: 2000,
      showConfirmButton: false,
    });

    closeModal();
  } catch (error) {
    console.error("Error al crear usuario:", error);
  }
};
</script>

<template>
  <div>
    <button @click="openModal" class="bg-blue-500 text-white px-4 py-2 rounded">
      Crear Usuario
    </button>

    <div
      v-if="isOpen"
      class="fixed inset-0 z-50 flex items-center justify-center bg-gray-900 bg-opacity-50"
    >
      <div class="relative p-4 w-full max-w-2xl max-h-full">
        <div class="relative bg-white rounded-lg shadow-sm dark:bg-gray-700">
          <div class="flex items-center justify-between p-4 border-b rounded-t dark:border-gray-600">
            <h3 class="text-xl font-semibold text-gray-900 dark:text-white">
              Crear Usuario
            </h3>
            <button
              type="button"
              @click="closeModal"
              class="text-gray-400 hover:bg-gray-200 hover:text-gray-900 rounded-lg text-sm w-8 h-8"
            >
              ✖
            </button>
          </div>

          <div class="p-4 space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-white">Nombre</label>
              <input
                v-model="user.name"
                type="text"
                class="w-full p-2 border rounded"
                :class="{ 'border-red-500': errors.name }"
              />
              <p v-if="errors.name" class="text-red-500 text-sm">{{ errors.name }}</p>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-white">Correo</label>
              <input
                v-model="user.email"
                type="email"
                class="w-full p-2 border rounded"
                :class="{ 'border-red-500': errors.email }"
              />
              <p v-if="errors.email" class="text-red-500 text-sm">{{ errors.email }}</p>
            </div>

            <div class="flex justify-end space-x-2">
              <button @click="closeModal" class="bg-gray-500 text-white px-4 py-2 rounded">
                Cancelar
              </button>
              <button @click="createUser" class="bg-blue-500 text-white px-4 py-2 rounded">
                Crear Usuario
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
