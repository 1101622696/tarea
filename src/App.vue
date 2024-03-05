

<script setup>

import Swal from 'sweetalert2';

function closealert() {
  alerta.value=""
  }
import { ref } from "vue";
let data_user = ref(true);
let data_contened = ref(false);
let data_retiros=ref(false);
const users = ref([]);
const valor = ref("");
const user = ref("");
const contra = ref("");
const plus = ref("");
const operacion = ref("");
let alerta=ref("")
const desgloseBilletes = ref({ 50000: 0, 20000: 0, 10000: 0 });

function cambiardivs() {
  data_user.value = false
  data_contened.value = true
}
const validar = () => {
  if (user.value === "") {
    mostrarError("Nombre de usuario no puede estar vacio");
  } else if (valor.value === "") {
    mostrarError("Debe ingresar un valor");
  } else if (contra.value === "") {
    mostrarError("Ingrese una contraseña");
  } else {
    mostrarmensaje("Registro de usuario exitoso");
    users.value.push({
      nombre: user.value,
      contrasena: contra.value,
      saldo: valor.value
    })
    console.log(users.value);
    cambiardivs()
  }
}
const realizarOperacion = () => {
  if (operacion.value === "consignar") {
    consignar();
  } else if (operacion.value === "retirar") {
    pedirContrasena();
  }
};

const consignar = () => {
  if (isNaN(Number(plus.value)) || Number(plus.value) <= 0) {
    alerta.value="Ingrese un valor válido para la consignación";
  } else {
    valor.value = Number(valor.value) + Number(plus.value);
    mostrarmensaje(`Consignación exitosa. Nuevo saldo: $${valor.value}`);
  }
};

const pedirContrasena = () => {
  const inputPassword = prompt("Ingrese su contraseña para retirar");
  if (inputPassword === users.value.find(u => u.nombre === user.value)?.contrasena) {
    retirar();
  } else {
    mostrarError("Contraseña incorrecta. No se puede realizar el retiro");
  }
};

const retirar = () => {
  if (isNaN(Number(plus.value)) || Number(plus.value) <= 0) {
    mostrarError("Ingrese un valor válido para el retiro");
  } else if (Number(plus.value) % 10000 !== 0) {
    mostrarError("Solo se permiten retiros en múltiplos de 10000" );
  } else if (Number(plus.value) > Number(valor.value)) {
    mostrarError("No tiene suficiente saldo para realizar el retiro");
  } else {
    valor.value = Number(valor.value) - Number(plus.value);
    mostrarmensaje(`Retiro exitoso. Nuevo saldo: $${valor.value}`);
    actualizarDesgloseBilletes(Number(plus.value));
  }
};

const actualizarDesgloseBilletes = (monto) => {
  desgloseBilletes.value = { 50000: 0, 20000: 0, 10000: 0 };
  data_retiros.value=true;
  while (monto >= 50000) {
    desgloseBilletes.value[50000]++;
    monto -= 50000;
  }

  while (monto >= 20000) {
    desgloseBilletes.value[20000]++;
    monto -= 20000;
  }

  while (monto >= 10000) {
    desgloseBilletes.value[10000]++;
    monto -= 10000;
  }
};

function mostrarError(mensaje) {
  Swal.fire({
    icon: "error",
    title: mensaje,
    text: "Vuelve e intenta!",
  });
}
function mostrarmensaje(mensaje) {
  Swal.fire({
    icon: "success",
    title: mensaje,
    text: "Bien hecho",
  });
}

</script>



<template>
  <div class="principal">
    <div class="user_prin" id="user_prin" v-show="data_user">
      <p id="datos_t">Datos Registro </p>
      <input class="controls" type="number" id="pre" placeholder="Ingresar saldo inicial" v-model="valor" />
      <input class="controls" type="text" id="user" placeholder="Ingresar usuario" v-model="user" />
      <input class="controls" type="text" id="contra" placeholder="Ingresar contraseña" v-model="contra" />
      <button class="button2" @click="validar()">Agregar</button>
    </div>

    <div class="caja" id="cajaDiv" v-show="data_contened">
      <div class="meter">
        <h2 id="saldo_es">Tu saldo es ${{ valor }}</h2>
        <h4>Usuario actual: {{ user }}</h4>
        <form @submit.prevent="realizarOperacion">
          <select class="type_register" v-model="operacion">
            <option value="consignar">Consignar</option>
            <hr>
            <option value="retirar">Retirar</option>
          </select>
          <input class="controls" type="number" v-model="plus" placeholder="Ingrese un valor">
          <button class="button1">Agregar</button>
        </form>
      </div>

      <div class="retiros" v-show="data_retiros">
        <h2>Nomenclatura del retiro</h2>
        <table style="width: 100%;">
          <tbody id="t">
            <tr v-for="(cantidad, denominacion) in desgloseBilletes" :key="denominacion">
              <td>Entregado {{ cantidad }} billete(s) de {{ denominacion }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>



<style coped>
.principal{
  width:80vw;
  height:80vh;
  background-color: rgb(168, 168, 241);
}

.user_prin{
  width:70%;
  height:60%;
  margin-left: auto;
  margin-right: auto;
}
#datos_t{
  color:black;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
  font-size: x-large;
  font-weight: 300;
  letter-spacing: 2px;
}

.controls{
  width:10vw;
  height:5vh;
  background-color: beige;
  color:black
}

.button2{
  background-color: rgb(82, 73, 73);
  margin-left:10%
}

#saldo_es{
color:black;
letter-spacing: 1px;
}
h4{
color:black;
font-size: larger;
}

.type_register{
  background-color: azure;
  width:8vw;
  height:4vh;
  color:black;

}


</style>
