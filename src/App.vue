<script setup>
import { ref } from 'vue';

const materia = ref('');
const dia = ref('');
const horarioInicio = ref('');
const horarioFin = ref('');
const arregloFilas = ref([]);
const columnas = [
    'Hora',
    'Lunes',
    'Martes',
    'Miércoles',
    'Jueves',
    'Viernes',
    'Sábado',
];
const color = ref('#000000');
const materiaColors = {};

const agregarMateria = () => {
    if (horarioInicio.value >= horarioFin.value) {
        alert('La hora de inicio debe ser menor a la hora de fin');
        return;
    }

    const nuevaMateria = {
        materia: materia.value,
        dia: dia.value,
        horarioInicio: horarioInicio.value,
        horarioFin: horarioFin.value,
        color: color.value,
    };
    if (!materiaColors[nuevaMateria.materia]) {
        materiaColors[nuevaMateria.materia] = color.value;
    }
    // Asignamos el color de la materia a la propiedad color de nuevaMateria
    nuevaMateria.color = materiaColors[nuevaMateria.materia];

    // Buscamos si ya existe una fila con el mismo horario de inicio
    const filaExistente = arregloFilas.value.find((fila) =>
        fila[0].startsWith(nuevaMateria.horarioInicio)
    );
    if (filaExistente) {
        // Si existe, actualizamos el valor en la posición del día correspondiente
        const diaIndex = columnas.indexOf(nuevaMateria.dia);
        if (filaExistente[diaIndex] !== '') {
            // Si la posición ya está ocupada, agregamos una coma al final
            filaExistente[diaIndex] += ',';
        }
        filaExistente[diaIndex] += nuevaMateria.materia;
    } else {
        // Si no existe, creamos una nueva fila
        const fila = ['', '', '', '', '', '', ''];
        fila[0] = nuevaMateria.horarioInicio + '-' + nuevaMateria.horarioFin;
        const diaIndex = columnas.indexOf(nuevaMateria.dia);
        fila[diaIndex] = nuevaMateria.materia;
        arregloFilas.value.push(fila);
    }

    // Ordenamos las filas por hora de inicio
    arregloFilas.value.sort((a, b) => {
        const horaA = new Date('1970-01-01T' + a[0].split('-')[0]);
        const horaB = new Date('1970-01-01T' + b[0].split('-')[0]);
        return horaA - horaB;
    });
    console.log(arregloFilas);
    // Limpiamos los campos del formulario
    materia.value = '';
    dia.value = '';
    horarioInicio.value = '';
    horarioFin.value = '';
};
const getColor = (materia) => {
    if (materia in materiaColors) {
        return materiaColors[materia];
    }
    return 'transparent';
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
                    <label for="color" class="form-label">Color</label>
                    <input
                        v-model.trim="color"
                        type="color"
                        class="form-control"
                        id="color"
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
            <table class="table table-bordered text-center table-responsive">
                <thead>
                    <tr>
                        <th style="width: 10%">Hora</th>
                        <th style="width: 15%">Lunes</th>
                        <th style="width: 15%">Martes</th>
                        <th style="width: 15%">Miércoles</th>
                        <th style="width: 15%">Jueves</th>
                        <th style="width: 15%">Viernes</th>
                        <th style="width: 15%">Sábado</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(fila, index) in arregloFilas" :key="index">
                        <td
                            v-for="(columna, index) in fila"
                            :key="index"
                            :style="{
                                backgroundColor: getColor(columna),
                                color:
                                    columna.indexOf(',') > -1
                                        ? 'red'
                                        : 'inherit',
                            }"
                        >
                            {{ columna }}
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>
<style scoped></style>
