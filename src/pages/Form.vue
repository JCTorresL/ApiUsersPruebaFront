<template>
    <div>
        <Dialog :visible="props.visible" modal :style="{ width: '25rem', background: 'white', padding: '1rem' }"
            :breakpoints="{ '1199px': '75vw', '575px': '90vw' }" :dismissableMask="false" :closeOnEscape="false"
            :closable="false">

            <template #header>
                <div class="d-flex justify-content-between align-items-center w-100">
                    <span>{{ props.title }}</span>
                    <svg @click="closeModal" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor"
                        class="cursor-pointer" style="height: 24px; width: 24px; margin-left: auto;">
                        <path fill-rule="evenodd"
                            d="M10 8.586l-4.293-4.293-1.414 1.414L8.586 10l-4.293 4.293 1.414 1.414L10 11.414l4.293 4.293 1.414-1.414L11.414 10l4.293-4.293-1.414-1.414L10 8.586z"
                            clip-rule="evenodd" />
                    </svg>
                </div>
            </template>

            <div class="b-2">
                <label for="nombre" class="label-form">Nombre</label>
                <input type="text" class="form-control" id="nombre" v-model="info.nombre" required>
                <span v-if="errors.nombre" class="text-danger">{{ errors.nombre[0] }}</span>
            </div>

            <div class="b-2">
                <label for="ciudad" class="label-form">Ciudad</label>
                <input type="text" class="form-control" id="ciudad" v-model="info.ciudad" required>
                <span v-if="errors.ciudad" class="text-danger">{{ errors.ciudad[0] }}</span>
            </div>

            <div class="b-2">
                <label for="correo" class="label-form">Correo</label>
                <input type="email" class="form-control" id="correo" v-model="info.correo">
                <span v-if="errors.correo" class="text-danger">{{ errors.correo[0] }}</span>
            </div>

            <div class="b-2">
                <label for="telefono" class="label-form">Telefono</label>
                <input type="text" class="form-control" id="telefono" v-model="info.telefono">
                <span v-if="errors.telefono" class="text-danger">{{ errors.telefono[0] }}</span>
            </div>

            <div class="d-flex justify-content-end gap-2 mt-2">
                <button type="submit"
                    :class="props.id == 0 ? 'btn btn-sm btn-outline-success' : 'btn btn-sm btn-outline-warning'"
                    @click="props.id == 0 ? createUser() : editUser()">
                    {{ props.id == 0 ? 'Crear' : 'Editar' }}
                </button>
                <button type="button" severity="secondary" @click="closeModal"
                    class="btn btn-sm btn-outline-dark">Cerrar</button>
            </div>
        </Dialog>
    </div>

</template>

<script setup>
import Dialog from "primevue/dialog";
import axios from "axios";
import Swal from "sweetalert2";
import { ref, onUpdated } from "vue";

const props = defineProps({
    visible: {
        Boolean,
        default: false
    },
    title: {
        type: String,
        required: true
    },
    id: {
        type: Number,
        required: true
    }
})

const emit = defineEmits(['close'])
const closeModal = () => {
    info.value = {}
    errors.value = {}
    emit('close', false)
};


//*Informacion del usuario
const info = ref(Object)
const errors = ref(Object)

onUpdated(() => {
    getInfo()
})

const getInfo = () => {

    if (props.id != 0) {
        axios.get(`http://127.0.0.1:8000/api/users/${props.id}`).then(({ data }) => {
            info.value = data.data
        })
    } else {

        info.value = {}
        errors.value = {}

    }

}

const editUser = () => {

    axios.put(`http://127.0.0.1:8000/api/users/${props.id}`, info.value).then(() => {

        closeModal()

        Swal.fire({
            position: "center",
            icon: "success",
            title: "Registro Editado",
            showConfirmButton: false,
            timer: 1500
        })

    }).catch(() => {

        closeModal()

        Swal.fire({
            position: "center",
            icon: "error",
            title: "Algo paso",
            showConfirmButton: false,
            timer: 1500
        })

    })
}

const createUser = () => {

    axios.post(`http://127.0.0.1:8000/api/users`, info.value).then(({ data }) => {

        if (!data.errors) {
            console.log('asdasd');
            closeModal()
            Swal.fire({
                position: "center",
                icon: "success",
                title: "Registro Creado",
                showConfirmButton: false,
                timer: 1500
            })
        } else {
            errors.value = data.errors
        }

    })
}


</script>

<style scoped>
input {
    background-color: white;
    color: black;
}
</style>