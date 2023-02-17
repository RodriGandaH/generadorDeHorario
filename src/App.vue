<script setup>
import { ref } from 'vue';

import html2canvas from 'html2canvas';

const materia = ref('');
const dia = ref('');
const aula = ref('');
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
        aula: aula.value,
        dia: dia.value,
        horarioInicio: horarioInicio.value,
        horarioFin: horarioFin.value,
        color: color.value,
    };
    if (!materiaColors[nuevaMateria.materia.split('-')[0]]) {
        //texto.split('-')[0]
        materiaColors[nuevaMateria.materia.split('-')[0]] = color.value;
    }
    // Asignamos el color de la materia a la propiedad color de nuevaMateria
    nuevaMateria.color = materiaColors[nuevaMateria.materia.split('-')[0]];

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
        filaExistente[diaIndex] +=
            nuevaMateria.materia + '-' + nuevaMateria.aula;
    } else {
        // Si no existe, creamos una nueva fila
        const fila = ['', '', '', '', '', '', ''];
        fila[0] = nuevaMateria.horarioInicio + '-' + nuevaMateria.horarioFin;
        const diaIndex = columnas.indexOf(nuevaMateria.dia);
        fila[diaIndex] = nuevaMateria.materia + '-' + nuevaMateria.aula;
        arregloFilas.value.push(fila);
    }

    // Ordenamos las filas por hora de inicio
    arregloFilas.value.sort((a, b) => {
        const horaA = new Date('1970-01-01T' + a[0].split('-')[0]);
        const horaB = new Date('1970-01-01T' + b[0].split('-')[0]);
        return horaA - horaB;
    });
    console.log(arregloFilas.value);
    // Limpiamos los campos del formulario

    aula.value = '';
    dia.value = '';
    horarioInicio.value = '';
    horarioFin.value = '';
};
const getColor = (materia) => {
    if (materia.split('-')[0] in materiaColors) {
        return materiaColors[materia.split('-')[0]];
    }
    return 'transparent';
};
const exportarTabla = () => {
    // Capturamos el elemento de la tabla
    const tabla = document.querySelector('#tabla');

    // Usamos html2canvas para capturar una imagen de la tabla
    html2canvas(tabla).then((canvas) => {
        // Creamos un enlace para descargar la imagen
        const enlace = document.createElement('a');
        enlace.download = 'horario.png';
        enlace.href = canvas.toDataURL();

        // Agregamos el enlace al documento y lo hacemos clic automáticamente
        document.body.appendChild(enlace);
        enlace.click();

        // Eliminamos el enlace del documento
        document.body.removeChild(enlace);
    });
};
const reemplazarComa = () => {
    materia.value = materia.value.replace(/,/g, '-');
};
const limpiarMateria = (e) => {
    materia.value = '';

    e.preventDefault();
};
</script>

<template>
    <div class="container row m-auto">
        <h1 class="text-center">HoarioApp</h1>
        <div class="col-12 col-md-4">
            <form @submit.prevent="agregarMateria">
                <div
                    class="d-flex justify-content-end"
                    @click="limpiarMateria"
                    style=""
                >
                    <div
                        class="text-end"
                        style="
                            background-color: #ffc433;
                            width: 40px;
                            border: 1px solid black;
                            border-radius: 5px;
                        "
                    >
                        <a href="">
                            <svg
                                fill="#000"
                                width="30px"
                                height="30px"
                                viewBox="0 0 32 32"
                                id="icon"
                                xmlns="http://www.w3.org/2000/svg"
                            >
                                <rect
                                    x="20"
                                    y="18"
                                    width="6"
                                    height="2"
                                    transform="translate(46 38) rotate(-180)"
                                />
                                <rect
                                    x="24"
                                    y="26"
                                    width="6"
                                    height="2"
                                    transform="translate(54 54) rotate(-180)"
                                />
                                <rect
                                    x="22"
                                    y="22"
                                    width="6"
                                    height="2"
                                    transform="translate(50 46) rotate(-180)"
                                />
                                <path
                                    d="M17.0029,20a4.8952,4.8952,0,0,0-2.4044-4.1729L22,3,20.2691,2,12.6933,15.126A5.6988,5.6988,0,0,0,7.45,16.6289C3.7064,20.24,3.9963,28.6821,4.01,29.04a1,1,0,0,0,1,.96H20.0012a1,1,0,0,0,.6-1.8C17.0615,25.5439,17.0029,20.0537,17.0029,20ZM11.93,16.9971A3.11,3.11,0,0,1,15.0041,20c0,.0381.0019.208.0168.4688L9.1215,17.8452A3.8,3.8,0,0,1,11.93,16.9971ZM15.4494,28A5.2,5.2,0,0,1,14,25H12a6.4993,6.4993,0,0,0,.9684,3H10.7451A16.6166,16.6166,0,0,1,10,24H8a17.3424,17.3424,0,0,0,.6652,4H6c.031-1.8364.29-5.8921,1.8027-8.5527l7.533,3.35A13.0253,13.0253,0,0,0,17.5968,28Z"
                                />
                                <rect
                                    id="_Transparent_Rectangle_"
                                    data-name="&lt;Transparent Rectangle&gt;"
                                    class="cls-1"
                                    width="32"
                                    height="32"
                                />
                            </svg>
                        </a>
                    </div>
                </div>
                <div class="mb-3">
                    <label for="materia" class="form-label">Materia</label>
                    <input
                        v-model.trim="materia"
                        type="text"
                        class="form-control"
                        id="materia"
                        @input="reemplazarComa"
                        required
                    />
                </div>
                <div class="mb-3">
                    <label for="aula" class="form-label">Aula</label>
                    <input
                        v-model.trim="aula"
                        type="text"
                        class="form-control"
                        id="aula"
                        @input="reemplazarComa"
                        required
                    />
                </div>
                <div class="mb-3">
                    <label for="color" class="form-label">Color</label>
                    <input
                        v-model.trim="color"
                        type="color"
                        class="form-control"
                        id="color"
                        size="30"
                        title="Seleccionar color"
                        style="
                            appearance: none;
                            height: 40px;
                            border-radius: 5px;
                            padding: 0;
                        "
                        required
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
                <div class="mt-3 d-flex justify-content-between">
                    <button type="submit" class="btn btn-dark">Agregar</button>
                    <a class="btn btn-primary" @click="exportarTabla"
                        >Descargar</a
                    >
                </div>
            </form>
        </div>

        <div class="col-12 col-md-8 table-responsive mt-3">
            <table class="table table-bordered text-center" id="tabla">
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
                                backgroundColor: getColor(
                                    columna.split('-')[0]
                                ),
                                color:
                                    columna.indexOf(',') > -1
                                        ? 'red'
                                        : 'inherit',
                                backgroundColor:
                                    columna.indexOf(',') > -1
                                        ? 'transparent'
                                        : getColor(columna.split('-')[0]),
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
<style scoped>
.cls-1 {
    fill: none;
}
</style>
