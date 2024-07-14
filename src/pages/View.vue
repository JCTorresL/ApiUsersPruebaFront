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

            <div>
                <span><b>Nombre:</b> {{ info.nombre }}</span><br>
                <span><b>Ciudad:</b> {{ info.ciudad }}</span><br>
                <span><b>Correo:</b> {{ info.correo ? info.correo : 'no hay registro' }}</span><br>
                <span><b>Telefono:</b> {{ info.telefono ? info.telefono : 'no hay registro' }}</span>
            </div>

            <div></div>
            <div></div>

            <div class="d-flex justify-content-end gap-2">
                <button type="button" severity="secondary" @click="closeModal"
                    class="btn btn-sm btn-outline-dark">Cerrar</button>
            </div>
        </Dialog>
    </div>

</template>

<script setup>
import Dialog from "primevue/dialog";
import { ref, onUpdated } from "vue";
import axios from "axios";

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
const closeModal = () => emit('close', false);


//*Informacion del usuario
const info = ref(Object)

onUpdated(() => {
    getInfo()
})

const getInfo = () => {

    axios.get(`http://127.0.0.1:8000/api/users/${props.id}`).then(({ data }) => {
        info.value = data.data
    })

}

</script>

<style scoped></style>