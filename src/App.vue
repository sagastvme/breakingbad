<template>
  <h1 class="text-4xl mt-8 flex  w-full items-center justify-center">
    <img class="mr-4" width="100" src="/src/assets/breaking.svg" alt="Website logo">
    <span class=" text-[#0d3e10] text-center">Breaking Bad</span>
  </h1>


  <div v-if="loaded"
       class="gap-14 text-2xl  flex flex-col items-center justify-center mx-auto text-center mt-32 w-full max-w-sm">
    <p class=" w-full text-center">The word you entered was {{ validElement.enteredString }}</p>
    <div class="flex text-4xl flex-row text-[#1f6032] ">
      <p class=" ">{{ validElement.initialString }}</p>
      <span class="px-1 border border-black text-white bg-[#1f6032]">{{ validElement.value }}</span>
      <p class=" ">{{ validElement.finalString }}</p>
    </div>
    <p class=" w-full">The value that matched your word was <span class="text-[#1f6032] underline">{{ validElement.fullValue }}</span></p>
    <button @click="resetNameVars" type="button"
            class="text-white bg-gradient-to-r from-[#1f6032] via-green-700 to-green-800 hover:bg-gradient-to-br focus:ring-4 focus:outline-none focus:ring-green-300 dark:focus:ring-green-800 shadow-lg shadow-green-500/50 dark:shadow-lg dark:shadow-green-800/80 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2">
      Go back
    </button>
  </div>



  <form @submit.prevent="breakBad" v-else class="flex items-center justify-center mx-auto mt-32 w-full max-w-sm">
    <div class="flex items-center border-b border-[#1f6032] py-2">
      <label for="name">Word</label>
      <input ref="nameInput"
             class="text-[#1f6032] appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none"
             type="text" id="name" aria-label="Full name">
      <input value="Lets cook!" type="submit"
             class="text-white bg-gradient-to-r from-[#1f6032] via-green-700 to-green-800 hover:bg-gradient-to-br focus:ring-4 focus:outline-none focus:ring-green-300 dark:focus:ring-green-800 shadow-lg shadow-green-500/50 dark:shadow-lg cursor-pointer dark:shadow-green-800/80 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2">


    </div>
  </form>


  <div v-if="notFound || stringNotValid" role="alert" class="shadow-2xl w-full max-w-sm mx-auto mt-32">
    <div class="bg-red-500 text-white font-bold rounded-t px-4 py-2 ">
      Error
    </div>
    <div class="border border-t-0 border-red-400 rounded-b bg-red-100 px-4 py-3 text-red-700">
      <p>{{ message }}</p>
    </div>
  </div>


</template>

<script setup>
import {ref, onMounted} from "vue";

const periodicTableElements = [
  {symbol: "H", name: "Hydrogen"},
  {symbol: "He", name: "Helium"},
  {symbol: "Li", name: "Lithium"},
  {symbol: "Be", name: "Beryllium"},
  {symbol: "B", name: "Boron"},
  {symbol: "C", name: "Carbon"},
  {symbol: "N", name: "Nitrogen"},
  {symbol: "O", name: "Oxygen"},
  {symbol: "F", name: "Fluorine"},
  {symbol: "Ne", name: "Neon"},
  {symbol: "Na", name: "Sodium"},
  {symbol: "Mg", name: "Magnesium"},
  {symbol: "Al", name: "Aluminum"},
  {symbol: "Si", name: "Silicon"},
  {symbol: "P", name: "Phosphorus"},
  {symbol: "S", name: "Sulfur"},
  {symbol: "Cl", name: "Chlorine"},
  {symbol: "Ar", name: "Argon"},
  {symbol: "K", name: "Potassium"},
  {symbol: "Ca", name: "Calcium"},
  {symbol: "Sc", name: "Scandium"},
  {symbol: "Ti", name: "Titanium"},
  {symbol: "V", name: "Vanadium"},
  {symbol: "Cr", name: "Chromium"},
  {symbol: "Mn", name: "Manganese"},
  {symbol: "Fe", name: "Iron"},
  {symbol: "Co", name: "Cobalt"},
  {symbol: "Ni", name: "Nickel"},
  {symbol: "Cu", name: "Copper"},
  {symbol: "Zn", name: "Zinc"},
  {symbol: "Ga", name: "Gallium"},
  {symbol: "Ge", name: "Germanium"},
  {symbol: "As", name: "Arsenic"},
  {symbol: "Se", name: "Selenium"},
  {symbol: "Br", name: "Bromine"},
  {symbol: "Kr", name: "Krypton"},
  {symbol: "Rb", name: "Rubidium"},
  {symbol: "Sr", name: "Strontium"},
  {symbol: "Y", name: "Yttrium"},
  {symbol: "Zr", name: "Zirconium"},
  {symbol: "Nb", name: "Niobium"},
  {symbol: "Mo", name: "Molybdenum"},
  {symbol: "Tc", name: "Technetium"},
  {symbol: "Ru", name: "Ruthenium"},
  {symbol: "Rh", name: "Rhodium"},
  {symbol: "Pd", name: "Palladium"},
  {symbol: "Ag", name: "Silver"},
  {symbol: "Cd", name: "Cadmium"},
  {symbol: "In", name: "Indium"},
  {symbol: "Sn", name: "Tin"},
  {symbol: "Sb", name: "Antimony"},
  {symbol: "Te", name: "Tellurium"},
  {symbol: "I", name: "Iodine"},
  {symbol: "Xe", name: "Xenon"},
  {symbol: "Cs", name: "Cesium"},
  {symbol: "Ba", name: "Barium"},
  {symbol: "La", name: "Lanthanum"},
  {symbol: "Ce", name: "Cerium"},
  {symbol: "Pr", name: "Praseodymium"},
  {symbol: "Nd", name: "Neodymium"},
  {symbol: "Pm", name: "Promethium"},
  {symbol: "Sm", name: "Samarium"},
  {symbol: "Eu", name: "Europium"},
  {symbol: "Gd", name: "Gadolinium"},
  {symbol: "Tb", name: "Terbium"},
  {symbol: "Dy", name: "Dysprosium"},
  {symbol: "Ho", name: "Holmium"},
  {symbol: "Er", name: "Erbium"},
  {symbol: "Tm", name: "Thulium"},
  {symbol: "Yb", name: "Ytterbium"},
  {symbol: "Lu", name: "Lutetium"},
  {symbol: "Hf", name: "Hafnium"},
  {symbol: "Ta", name: "Tantalum"},
  {symbol: "W", name: "Tungsten"},
  {symbol: "Re", name: "Rhenium"},
  {symbol: "Os", name: "Osmium"},
  {symbol: "Ir", name: "Iridium"}
];

const message = ref('')
const nameInput = ref(null);
const loaded = ref(false);
const notFound = ref(false)
const stringNotValid = ref(false)
let validElement = {
  value: '',
  position: null,
  initialString: null,
  finalString: null,
  fullValue: null,
  enteredString: null
}


const breakBad = () => {
  resetErrorVars()
  validElement.enteredString = nameInput.value.value
  let enteredName = nameInput.value.value.trim().toLowerCase();
  if (enteredName.length > 0) {
    processName(enteredName)
  } else {
    stringNotValid.value = true
    message.value = 'The name you entered is not valid'
  }
  nameInput.value.value = ""; // Clear the input after submitting
};

const processName = (enteredName) => {

  periodicTableElements.map((element) => {
    let index = enteredName.indexOf(element.symbol.toLowerCase())
    if (index != -1) {
      if (element.symbol.length > validElement.value.length) {
        validElement.value = element.symbol
        validElement.position = index
        validElement.fullValue = element.name
      }
    }
  })
  if (validElement.position !== null) {

    let finalPositionValidElement = parseInt(validElement.position + validElement.value.length)
    validElement.initialString = enteredName.substring(0, validElement.position)

    if (validElement.initialString !== '') {
      validElement.initialString = validElement.initialString[0].toUpperCase() + validElement.initialString.substring(1)
    }
    if (enteredName.length !== finalPositionValidElement) {
      validElement.finalString = enteredName.substring(finalPositionValidElement)
    }


    loaded.value = true
  } else {

    notFound.value = true
    message.value = 'Sorry we couldnt find any value for the name you entered'
  }
}
const resetNameVars = () => {

  loaded.value = false
  validElement.value = ''
  validElement.position = null
  validElement.initialString = null
  validElement.finalString = null
  validElement.fullValue = null
  validElement.enteredString = null

  setTimeout(() => {

    nameInput.value.focus();
  }, 1)

}

const resetErrorVars = () => {

  message.value = ''
  notFound.value = false
  stringNotValid.value = false
}

onMounted(() => {
  // Focus on the input element when the component is mounted
  nameInput.value.focus();

});
</script>


