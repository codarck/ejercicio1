<script setup>
import { Head, Link } from '@inertiajs/vue3';
import { ref, onMounted, computed } from "vue";


import icoDelete from '@/Components/icono/Delete.vue'

import icoPencil from '@/Components/icono/Pencil.vue'
import icoPlus from '@/Components/icono/Plus.vue'
import CreateModal from '@/Pages/component/CreateModal.vue';
import DeleteModal from '@/Pages/component/DeleteModal.vue';
import EditModal from '@/Pages/component/EditModal.vue';
defineProps({
    canLogin: {
        type: Boolean,
    },
    canRegister: {
        type: Boolean,
    },
    laravelVersion: {
        type: String,
        required: true,
    },
    phpVersion: {
        type: String,
        required: true,
    },
});

function handleImageError() {
    document.getElementById('screenshot-container')?.classList.add('!hidden');
    document.getElementById('docs-card')?.classList.add('!row-span-1');
    document.getElementById('docs-card-content')?.classList.add('!flex-row');
    document.getElementById('background')?.classList.add('!hidden');
}

const posts = ref([]);
const currentPage = ref(1);
const itemsPerPage = 10; // Muestra 10 posts por página

// Cargar datos de la API
onMounted(async () => {
  const { data } = await axios.get("https://jsonplaceholder.typicode.com/users");
  posts.value = data;
});
// Cálculo de los posts visibles en la página actual
const paginatedPosts = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return posts.value.slice(start, start + itemsPerPage);
});

// Número total de páginas
const totalPages = computed(() => Math.ceil(posts.value.length / itemsPerPage));

// Funciones para cambiar de página
const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};
const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};


const selectedPutId = ref(null);
const selectedDeleteId = ref(null);
const CreatModal = (id) => {
  selectedPutId.value = id;

};
const DeletModal =(id)=>{
    selectedDeleteId.value = id;
};
// Función para cerrar el modal
const closeModal = () => {
  selectedPutId.value = null;
};
const DeletcloseModal = () => {
    selectedDeleteId.value = null;
};
</script>

<template>

<button data-drawer-target="default-sidebar" data-drawer-toggle="default-sidebar" aria-controls="default-sidebar" type="button" class="inline-flex items-center p-2 mt-2 ms-3 text-sm text-gray-500 rounded-lg sm:hidden hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-gray-200 dark:text-gray-400 dark:hover:bg-gray-700 dark:focus:ring-gray-600">
   <span class="sr-only">Open sidebar</span>
   <svg class="w-6 h-6" aria-hidden="true" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
   <path clip-rule="evenodd" fill-rule="evenodd" d="M2 4.75A.75.75 0 012.75 4h14.5a.75.75 0 010 1.5H2.75A.75.75 0 012 4.75zm0 10.5a.75.75 0 01.75-.75h7.5a.75.75 0 010 1.5h-7.5a.75.75 0 01-.75-.75zM2 10a.75.75 0 01.75-.75h14.5a.75.75 0 010 1.5H2.75A.75.75 0 012 10z"></path>
   </svg>
</button>

<aside id="default-sidebar" class="fixed top-0 left-0 z-40 w-64 h-screen transition-transform -translate-x-full sm:translate-x-0" aria-label="Sidebar">
   <div class="h-full px-3 py-4 overflow-y-auto bg-gray-50 dark:bg-gray-800">
      <ul class="space-y-2 font-medium">
         <li>
            <a href="#" class="flex items-center p-2 text-gray-900 rounded-lg dark:text-white hover:bg-gray-100 dark:hover:bg-gray-700 group">
               <svg class="w-5 h-5 text-gray-500 transition duration-75 dark:text-gray-400 group-hover:text-gray-900 dark:group-hover:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 22 21">
                  <path d="M16.975 11H10V4.025a1 1 0 0 0-1.066-.998 8.5 8.5 0 1 0 9.039 9.039.999.999 0 0 0-1-1.066h.002Z"/>
                  <path d="M12.5 0c-.157 0-.311.01-.565.027A1 1 0 0 0 11 1.02V10h8.975a1 1 0 0 0 1-.935c.013-.188.028-.374.028-.565A8.51 8.51 0 0 0 12.5 0Z"/>
               </svg>
               <span class="ms-3">Main</span>
            </a>
         </li>

      </ul>
   </div>
</aside>

<div class="p-4 sm:ml-64">

    <Modal />
    <div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">USUARIOS</h1>
    <CreateModal></CreateModal>
    <table class="w-full border-collapse border border-gray-400">
      <thead>
        <tr>
          <th class="border p-2">Nombre</th>
          <th class="border p-2">Correo</th>
          <th class="border p-2">

          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in paginatedPosts" :key="post.id">
          <td class="border p-2">{{ post.name }}</td>
          <td class="border p-2">{{ post.email }}</td>
          <th class="border p-2 ">
          <div class="flex">

          <button type="button" @click="CreatModal(post.id)" data-modal-target="editar_usuario" data-modal-toggle="editar_usuario" class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-2 py-2.5 me-2 mb-2 dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none dark:focus:ring-blue-800"> <icoPencil/> </button>
          <button type="button" @click="DeletModal(post.id)" data-modal-target="eliminar_usuario" data-modal-toggle="editar_usuario" class="text-white bg-red-700 hover:bg-red-800 focus:ring-4 focus:ring-red-300 font-medium rounded-lg text-sm px-2 py-2.5 me-2 mb-2 dark:bg-red-600 dark:hover:bg-red-700 focus:outline-none dark:focus:ring-red-800"> <icoDelete/> </button>


          </div>
          </th>
        </tr>
      </tbody>
    </table>
<!-- Mostrar modal solo cuando hay un ID seleccionado -->
<EditModal v-if="selectedPutId" :PutId="selectedPutId" @close="closeModal" />
<DeleteModal v-if="selectedDeleteId" :DeleteId="selectedDeleteId" @close="DeletcloseModal" />
    <!-- Paginación -->
    <div class="flex justify-center space-x-4 mt-4">
      <button
        @click="prevPage"
        :disabled="currentPage === 1"
        class="px-4 py-2 bg-blue-500 text-white rounded disabled:bg-gray-400"
      >
        Anterior
      </button>

      <span class="px-4 py-2">Página {{ currentPage }} de {{ totalPages }}</span>

      <button
        @click="nextPage"
        :disabled="currentPage === totalPages"
        class="px-4 py-2 bg-blue-500 text-white rounded disabled:bg-gray-400"
      >
        Siguiente
      </button>
    </div>
  </div>



</div>

</template>
