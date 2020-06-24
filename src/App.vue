<template>
  <div id="app">
    <div class="leftSide">
      <img src="./assets/logo.png" />
      <h1>Footerator v1.1</h1>
      <h2>Wygeneruj stopki na podstawie pliku CSV!</h2>
    </div>
    <div class="rightSide">
      <InputForm />
    </div>
  </div>
</template>

<script>
import InputForm from "./components/InputForm.vue";
import "./scss/global.scss";
export default {
  name: "App",
  components: {
    InputForm,
  },
};
</script>
