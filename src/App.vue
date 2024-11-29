<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      fields: ["ID", "Titulo", "Descripcion", "Estado"],
      ID: "",
      Titulo: "",
      Descripcion: "",
      Estado: "Pendiente",
      listardatos: [],
      editingTask: null, 
    };
  },
  mounted() {
    this.obtenerTareas(); 
  },
  methods: {
    obtenerTareas() {
      axios
        .get("http://localhost:3000/api/tasks")
        .then((response) => {
          this.listardatos = response.data;
          console.log(this.listardatos);
        })
        .catch((error) => console.error(error));
    },
    crearTarea() {
      const nuevaTarea = {
        Titulo: this.Titulo,
        Descripcion: this.Descripcion,
        Estado: this.Estado,
      };
      axios
        .post("http://localhost:3000/api/tasks", nuevaTarea)
        .then(() => {
          this.obtenerTareas();
          this.limpiarFormulario();
        })
        .catch((error) => console.error(error));
    },
    editarTarea(tarea) {
      this.editingTask = { ...tarea }; 
      this.Titulo = tarea.Titulo;
      this.Descripcion = tarea.Descripcion;
      this.Estado = tarea.Estado;
    },
    actualizarTarea() {
      if (this.editingTask) {
        axios
          .put(`http://localhost:3000/api/tasks/${this.editingTask.ID}`, {
            Titulo: this.Titulo,
            Descripcion: this.Descripcion,
            Estado: this.Estado,
          })
          .then(() => {
            this.obtenerTareas();
            this.limpiarFormulario();
          })
          .catch((error) => console.error(error));
      }
    },
    eliminarTarea(id) {
      axios
        .delete(`http://localhost:3000/api/tasks/${id}`)
        .then(() => {
          this.obtenerTareas();
        })
        .catch((error) => console.error(error));
    },
    limpiarFormulario() {
      this.Titulo = "";
      this.Descripcion = "";
      this.Estado = "Pendiente";
      this.editingTask = null;
    },
  },
};
</script>

<template>
  <div class="flex flex-col items-center justify-center min-h-screen bg-gray-900 text-white py-6 px-4">
    <h1 class="text-3xl font-bold my-4">To-Do List</h1>

    <form @submit.prevent="editingTask ? actualizarTarea() : crearTarea()" class="w-full max-w-2xl p-4 bg-gray-800 rounded-lg shadow-lg">
      <div class="mb-3">
        <label for="titulo" class="form-label block">Título</label>
        <input
          v-model="Titulo"
          type="text"
          class="form-control w-full p-2 rounded-md bg-gray-700 text-white border border-gray-600"
          id="titulo"
          required
        />
      </div>
      <div class="mb-3">
        <label for="descripcion" class="form-label block">Descripción</label>
        <textarea
          v-model="Descripcion"
          class="form-control w-full p-2 rounded-md bg-gray-700 text-white border border-gray-600"
          id="descripcion"
          required
        ></textarea>
      </div>
      <div class="mb-3">
        <label for="estado" class="form-label block">Estado</label>
        <select
          v-model="Estado"
          class="form-select w-full p-2 rounded-md bg-gray-700 text-white border border-gray-600"
          id="estado"
          required
        >
          <option value="Pendiente">Pendiente</option>
          <option value="En Proceso">En Proceso</option>
          <option value="Completada">Completada</option>
        </select>
      </div>
      <button type="submit" class="btn bg-blue-600 hover:bg-blue-700 w-full p-3 rounded-md text-white">
        {{ editingTask ? "Actualizar Tarea" : "Crear Tarea" }}
      </button>
      <button
        v-if="editingTask"
        type="button"
        @click="limpiarFormulario"
        class="btn bg-gray-600 hover:bg-gray-700 w-full p-3 mt-2 rounded-md text-white"
      >
        Cancelar
      </button>
    </form>

    <table class="table mt-6 w-full max-w-4xl bg-gray-800 rounded-lg shadow-lg overflow-hidden">
      <thead>
        <tr>
          <th class="p-4 text-left">ID</th>
          <th class="p-4 text-left">Título</th>
          <th class="p-4 text-left">Descripción</th>
          <th class="p-4 text-left">Estado</th>
          <th class="p-4 text-left">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="tarea in listardatos" :key="tarea.ID" class="border-t border-gray-600">
          <td class="p-4">{{ tarea.ID }}</td>
          <td class="p-4">{{ tarea.Titulo }}</td>
          <td class="p-4">{{ tarea.Descripcion }}</td>
          <td class="p-4">{{ tarea.Estado }}</td>
          <td class="p-4">
            <button @click="editarTarea(tarea)" class="btn bg-yellow-500 hover:bg-yellow-600 p-2 rounded-md text-white">
              Editar
            </button>
            <button
              @click="eliminarTarea(tarea.ID)"
              class="btn bg-red-500 hover:bg-red-600 p-2 rounded-md text-white ml-2"
            >
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.container {
  padding: 20px;
}

button {
  margin-right: 10px;
}

@media (max-width: 768px) {
  h1 {
    font-size: 2xl;
  }

  table {
    font-size: 12px;
  }

  form {
    width: 90%;
  }
}
</style>
