<script setup>
import { ref, watch } from "vue";

const props = defineProps(["PutId"]);
const emit = defineEmits(["close"]);
import Swal from "sweetalert2"; // Importamos SweetAlert2
// Estado del usuario
const user = ref({ name: "", email: "" });
const errors = ref({ name: "", email: "" });

// Cargar datos cuando se recibe un PutId válido
watch(
  () => props.PutId,
  async (newId) => {
    if (newId) {
      try {
        const res = await fetch(`https://jsonplaceholder.typicode.com/users/${newId}`);
        if (!res.ok) throw new Error("No se pudo obtener el usuario");
        const data = await res.json();
        user.value = { name: data.name, email: data.email }; // Asigna los datos obtenidos
      } catch (error) {
        console.error("Error al obtener usuario:", error);
        user.value = { name: "Usuario no encontrado", email: "N/A" };
      }
    }
  },
  { immediate: true }
);

// Enviar datos actualizados al servidor (PUT)
const updateUser = async () => {
  try {
    const res = await fetch(`https://jsonplaceholder.typicode.com/users/${props.PutId}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(user.value),
    });

    if (!res.ok) throw new Error("Error al actualizar usuario");

    const data = await res.json();
    console.log("Usuario actualizado con éxito:", data);

    emit("close"); // Cerrar modal tras la actualización

    const result = await Swal.fire({
  position: "top-end",
  icon: "success",
  title: "Se Actualizo con exito",
  showConfirmButton: false,
  timer: 1500
});

  } catch (error) {
    console.error("Error al actualizar usuario:", error);
  }
};

// Validar y guardar cambios
const saveChanges = () => {
  errors.value = { name: "", email: "" };

  if (!user.value.name) errors.value.name = "El nombre es obligatorio";
  if (!user.value.email) errors.value.email = "El correo es obligatorio";
  if (!user.value.email.includes("@")) errors.value.email = "Correo no válido";

  if (!errors.value.name && !errors.value.email) {
    updateUser(); // Llamar a la función PUT si la validación pasa
  }
};
</script>


<template>
  <div
    v-if="PutId"
    class="fixed inset-0 z-50 flex items-center justify-center bg-gray-900 bg-opacity-50"
  >
    <div class="relative p-4 w-full max-w-2xl max-h-full">
      <div class="relative bg-white rounded-lg shadow-sm dark:bg-gray-700">
        <div class="flex items-center justify-between p-4 border-b rounded-t dark:border-gray-600">
          <h3 class="text-xl font-semibold text-gray-900 dark:text-white">
            Editar Usuario (ID: {{ PutId }})
          </h3>
          <button
            type="button"
            @click="emit('close')"
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
            <button @click="emit('close')" class="bg-gray-500 text-white px-4 py-2 rounded">
              Cancelar
            </button>
            <button @click="saveChanges()" class="bg-blue-500 text-white px-4 py-2 rounded">
              Guardar Cambios
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
