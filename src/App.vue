<template>
  <div class="principal">
    <div id="alerta">
    <textarea  v-if="alerta" v-model="alerta"> </textarea>
<button @click="closealert()" v-show="alerta">cerrar</button>
</div>

    <div class="user_prin" id="user_prin" v-show="data_user">
      <p style="font-weight: 900">Datos Registro </p>
      <input class="controls" type="number" id="pre" placeholder="Ingresar saldo inicial" v-model="valor" />
      <input class="controls" type="text" id="user" placeholder="Ingresar usuario" v-model="user" />
      <input class="controls" type="text" id="contra" placeholder="Ingresar contraseña" v-model="contra" />
      <button class="button2" @click="validar()">Agregar</button>
    </div>

    <div class="caja" id="cajaDiv" v-show="data_contened">
      <div class="meter">
        <h2>Tu saldo es ${{ valor }}</h2>
        <h4>Usuario actual {{ user }}</h4>
        <form @submit.prevent="realizarOperacion">
          <select class="type_register" v-model="operacion">
            <option value="consignar">Consignar</option>
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

<script setup>

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
    alerta.value="Nombre de usuario no puede estar vacio"
  } else if (valor.value === "") {
    alerta.value="Debe ingresar un valor"
  } else if (contra.value === "") {
    alerta.value="Ingrese una contraseña"
  } else {
    alerta.value="Registro de usuario exitoso"
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
    alerta.value=`Consignación exitosa. Nuevo saldo: $${valor.value}`;
  }
};

const pedirContrasena = () => {
  const inputPassword = prompt("Ingrese su contraseña para retirar");
  if (inputPassword === users.value.find(u => u.nombre === user.value)?.contrasena) {
    retirar();
  } else {
    alerta.value="Contraseña incorrecta. No se puede realizar el retiro";
  }
};

const retirar = () => {
  if (isNaN(Number(plus.value)) || Number(plus.value) <= 0) {
    alerta.value="Ingrese un valor válido para el retiro"
  } else if (Number(plus.value) % 10000 !== 0) {
    alerta.value="Solo se permiten retiros en múltiplos de 10000"
  } else if (Number(plus.value) > Number(valor.value)) {
    alerta.value="No tiene suficiente saldo para realizar el retiro";
  } else {
    valor.value = Number(valor.value) - Number(plus.value);
    alerta.value=`Retiro exitoso. Nuevo saldo: $${valor.value}`;
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

</script>


<style coped>
#alerta{
  position: absolute;
  top: 50%;
  left: 50%;
  width: 50px;
  height: 100px;

}
textarea{
  background:rgb(206, 65, 65);
  padding: 4%;
  font-size: large;
}
</style>