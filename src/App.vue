<template>
  <div id="app">
    <div class="leftSide">
      <img src="./assets/logo.png" />
      <h1>Footerator v1.1</h1>
      <h2>Wygeneruj stopki na podstawie pliku CSV!</h2>
      <h3>Instrukcja:</h3>
      <ol>
        <li>
          Przechodzimy pod adres
          <a class="exampleFile" href="" target="_blank">
            Plik wzorcowy
          </a>
        </li>
        <li>Uzupełniamy dane</li>
        <li>
          Po uzupełnieniu pliku wzorcowego w arkuszu google klikamy PLIK ->
          Pobierz -> wartość rozdzielana przecinkami (.csv, bieżący arkusz)
        </li>
        <li>
          Wracamy do arkusza google i czyścimy wszystkie dane (oprócz pierwszego
          wiersza który zawiera tytuł kolumn) - kwestie bezpieczeństwa danych
        </li>
        <li>
          Następnie przechodzimy do prawej strony aplikacji, wybieramy plik,
          sprawdzamy czy dane poprawnie się wczytały a następnie pobieramy
          paczkę ZIP zawierającą wygenerowane stopki
        </li>
      </ol>
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
    InputForm
  }
};
</script>
