<script setup>
import { ref } from 'vue';

const materia = ref('');
const dia = ref('');
const horarioInicio = ref('');
const horarioFin = ref('');
const materias = ref([]);
const dias = ['Lunes', 'Martes', 'Miércoles', 'Jueves', 'Viernes', 'Sábado'];

const agregarMateria = () => {
    if (horarioInicio.value >= horarioFin.value) {
        alert('La hora de inicio debe ser menor a la hora de fin');
        return;
    }

    let existe = false;
    // Verificar si existe una materia en el mismo horario y día
    for (let i = 0; i < materias.value.length; i++) {
        if (
            materias.value[i].dia === dia.value &&
            materias.value[i].horarioInicio === horarioInicio.value &&
            materias.value[i].horarioFin === horarioFin.value
        ) {
            materias.value[i].materia =
                materias.value[i].materia + ', ' + materia.value;
            materias.value[i].color = 'red';
            existe = true;
            break;
        }
    }

    if (!existe) {
        const nuevaMateria = {
            materia: materia.value,
            dia: dia.value,
            horarioInicio: horarioInicio.value,
            horarioFin: horarioFin.value,
        };
        materias.value.push(nuevaMateria);
    }

    materias.value.sort((a, b) => {
        const horaA = new Date('1970-01-01T' + a.horarioInicio);
        const horaB = new Date('1970-01-01T' + b.horarioInicio);
        return horaA - horaB;
    });

    console.log(materias.value);

    materia.value = '';
    dia.value = '';
    horarioInicio.value = '';
    horarioFin.value = '';
};
</script>

<template>
    <div class="container row m-auto">
        <h1 class="text-center">HoarioApp</h1>
        <div class="col-12 col-md-4">
            <form @submit.prevent="agregarMateria">
                <div class="mb-3">
                    <label for="materia" class="form-label">Materia</label>
                    <input
                        v-model.trim="materia"
                        type="text"
                        class="form-control"
                        id="materia"
                    />
                </div>
                <div class="mb-3">
                    <label class="form-label">
                        Dia
                        <select v-model="dia" class="form-select" required>
                            <option value="" disabled selected>
                                Selecciona el día
                            </option>
                            <option>Lunes</option>
                            <option>Martes</option>
                            <option>Miércoles</option>
                            <option>Jueves</option>
                            <option>Viernes</option>
                            <option>Sábado</option>
                        </select>
                    </label>
                </div>
                <div class="mb-3 row">
                    <div class="col-12 col-md-6">
                        <label>
                            Inicio
                            <input
                                v-model="horarioInicio"
                                type="time"
                                id="horarioInicio"
                                class="form-control"
                                required
                            />
                        </label>
                    </div>
                    <div class="col-12 col-md-6">
                        <label>
                            Fin
                            <input
                                v-model="horarioFin"
                                type="time"
                                id="horarioFin"
                                name="horarioFin"
                                class="form-control"
                                required
                            />
                        </label>
                    </div>
                </div>
                <button type="submit" class="btn btn-dark">Agregar</button>
            </form>
        </div>
        <div class="col-12 col-md-8">
            <table class="table">
                <thead>
                    <tr>
                        <th>Hora</th>
                        <th v-for="(dia, index) in dias" :key="index">
                            {{ dia }}
                        </th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(materia, index) in materias" :key="index">
                        <td>
                            {{ materia.horarioInicio }} -
                            {{ materia.horarioFin }}
                        </td>
                        <td v-for="(dia, index) in dias" :key="index">
                            <template v-if="materia.dia === dia">
                                <span :style="{ color: materia.color }">{{
                                    materia.materia
                                }}</span>
                            </template>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>
