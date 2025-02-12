<script setup>
import { ref, watch } from "vue";

const props = defineProps(["DeleteId"]);
const emit = defineEmits(["close"]);
import Swal from "sweetalert2"; // Importamos SweetAlert2
// Estado del usuario
const user = ref({ name: "", email: "" });

// Cargar datos cuando se recibe un DeleteId válido
watch(
  () => props.DeleteId,
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

// Función para eliminar usuario (DELETE)
const deleteUser = async () => {

  try {
    const res = await fetch(`https://jsonplaceholder.typicode.com/users/${props.DeleteId}`, {
      method: "DELETE",
    });

    if (!res.ok) throw new Error("Error al eliminar usuario");

    console.log(`Usuario con ID ${props.DeleteId} eliminado con éxito`);

    emit("close"); // Cerrar modal después de eliminar

    const result = await Swal.fire({
  position: "top-end",
  icon: "success",
  title: "Se elimino con exito",
  showConfirmButton: false,
  timer: 1500
});
  } catch (error) {
    console.error("Error al eliminar usuario:", error);
  }
};
</script>

<template>
  <div
    v-if="DeleteId"
    class="fixed inset-0 z-50 flex items-center justify-center bg-gray-900 bg-opacity-50"
  >
    <div class="relative p-4 w-full max-w-2xl max-h-full">
      <div class="relative bg-white rounded-lg shadow-sm dark:bg-gray-700">
        <div class="flex items-center justify-between p-4 border-b rounded-t dark:border-gray-600">
          <h3 class="text-xl font-semibold text-gray-900 dark:text-white">
            ELIMINAR Usuario (ID: {{ DeleteId }})
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
          <div class="py-5">
            ¿Desea eliminar a <strong>{{ user.name }}</strong> con el correo: <strong>{{ user.email }}</strong>?
          </div>
          <div class="flex justify-end space-x-2">
            <button @click="emit('close')" class="bg-gray-500 text-white px-4 py-2 rounded">
              Cancelar
            </button>
            <button @click="deleteUser()" class="bg-red-500 text-white px-4 py-2 rounded">
              Eliminar
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
