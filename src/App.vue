<script setup>
import { onMounted, ref, watch } from 'vue'
import axios from 'axios'
import Swal from 'sweetalert2'

const busqueda = ref('');
const usersPagination = ref(Object);
const paginate = ref(Object);

const openView = ref(false);
const openEdit = ref(false);
const openCreate = ref(false);

onMounted(() => {
  pagination()
})

const pagination = (current_page = 1, search = '') => {
  axios.get('http://127.0.0.1:8000/api/users?busqueda=' + search + "&page=" + current_page).then((info) => {
    usersPagination.value = info.data.data.data
    paginate.value = info.data.data
  })
}

const viewUser = () => {

  openView.value = true

}

const deleteUser = (id) => {

  Swal.fire({
    title: "Desea eliminar el usuario?",
    icon: "warning",
    showCancelButton: true,
    confirmButtonColor: "#3085d6",
    cancelButtonColor: "#d33",
    confirmButtonText: "Si",
    cancelButtonText: 'Cancelar',
  }).then((result) => {

    if (result.isConfirmed) {
      axios.delete(`http://127.0.0.1:8000/api/users/${id}`).then(() => {
        Swal.fire({
          title: "Eliminado!",
          text: "Usuario eliminado.",
          icon: "success"
        });

        pagination(1)
      })
    }
  });

}

const showModal = () => {
  this.$refs['my-modal'].show()
}

watch(busqueda, (newValue, oldValue) => {
  pagination(1, newValue)
});

</script>

<template>
  <div class="container">

    <div class="my-5 fs-6 text-light">
      <h1>Usuarios</h1>
    </div>

    <div class="mb-3 d-flex">

      <div class="input-group search">
        <input type="text" class="form-control" v-model="busqueda">
        <span class="input-group-text">
          <i class="fa-solid fa-magnifying-glass"></i>
        </span>
      </div>

      <div class="justify-content-end mx-2">
        <button class="btn btn-outline-light btn-md" title="crear">
          <i class="fa-solid fa-plus"></i>
        </button>
      </div>
    </div>

    <table class="table table-dark table-hover table-responsive">
      <thead class="">
        <tr>
          <th>#</th>
          <th>Nombre</th>
          <th>Ciudad</th>
          <th>Correo</th>
          <th>Telefono</th>
          <th>Operacion</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(user, i) in usersPagination" :key="user.id">
          <td>{{ i + 1 }}</td>
          <td>{{ user.nombre }}</td>
          <td>{{ user.ciudad }}</td>
          <td>{{ user.correo ? user.correo : 'no registra' }}</td>
          <td>{{ user.telefono ? user.telefono : 'no registra' }}</td>
          <td>
            <button type="button" class="btn btn-outline-info btn-sm" title="ver" @click="showModal">
              <i class="fa-solid fa-eye"></i>
            </button>&nbsp;
            <button class="btn btn-outline-warning btn-sm" title="editar">
              <i class="fa-solid fa-marker"></i>
            </button>&nbsp;
            <button class="btn btn-outline-danger btn-sm" title="eliminar" @click="deleteUser(user.id)">
              <i class="fa-solid fa-trash-can"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <nav>
      <ul class="pagination">
        <li class="page-item">
          <a :class="paginate.current_page > 1 ? 'page-link' : 'page-link disabled'" href="#" aria-label="Previous"
            @click="pagination(paginate.current_page - 1)">
            <span aria-hidden="true">&laquo;</span>
          </a>
        </li>
        <li class="page-item"><a class="page-link" href="#">{{ paginate.current_page }}</a></li>
        <li class="page-item">
          <a :class="paginate.current_page < paginate.last_page ? 'page-link' : 'page-link disabled'" href="#"
            aria-label="Next" @click="pagination(paginate.current_page + 1)">
            <span aria-hidden="true">&raquo;</span>
          </a>
        </li>
      </ul>
    </nav>

  </div>
</template>

<style scoped>
.container {
  width: 70rem;
}

.pagination {
  display: flex;
  justify-content: center;
}

.pagination .page-link {
  background-color: #212529 !important;
  color: white;
}

.pagination .page-link:hover {
  background-color: white !important;
  color: #212529;
}

.disabled {
  pointer-events: none;
  opacity: 0.6;
}

.search input,
.search span {
  background-color: #212529;
  color: white;
}
</style>
