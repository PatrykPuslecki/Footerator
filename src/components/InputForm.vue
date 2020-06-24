<template>
  <div class="form">
    <div class="panel-body">
      <div class="form-group">
        <label v-if="!parse_csv[0]" for="csv_file" class="control-label">
          Załącz plik CSV
        </label>
        <label v-if="parse_csv[0]" for="csv_file" class="control-label">
          Stopki są gotowe do pobrania!
        </label>
        <div v-if="!parse_csv[0]" class="inputContainer">
          <input
            type="file"
            id="csv_file"
            name="csv_file"
            accept=".csv"
            class="form-control"
            @change="loadCSV($event)"
          />
          <label for="csv_file">Wybierz plik</label>
        </div>
      </div>
      <div v-if="parse_csv[0]" class="button-parseCSV">
        <a href="#" class="btn-primary" v-on:click="saveToFile"
          >Pobierz wygenerowane stopki</a
        >
      </div>
      <div v-if="parse_csv[0]" class="tableHeading">
        <h2>Przesłane dane:</h2>
      </div>
      <table v-if="parse_csv">
        <thead>
          <tr>
            <th
              v-for="key in parse_header"
              :class="{ active: sortKey == key }"
              :key="key"
            >
              {{ key | capitalize }}
            </th>
          </tr>
        </thead>
        <tr v-for="csv in parse_csv" :key="csv.Telefon">
          <td v-for="key in parse_header" :key="key">
            {{ csv[key] }}
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
import JSZip from "jszip";
import { saveAs } from "file-saver";
export default {
  name: "InputForm",

  data() {
    return {
      channel_name: "",
      channel_fields: [],
      channel_entries: [],
      parse_header: [],
      parse_csv: [],
      parsed_footers: [],
      sortOrders: {},
      sortKey: "",
    };
  },
  filters: {
    capitalize: function(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
  },
  methods: {
    csvJSON(csv) {
      var vm = this;

      var lines = csv.split("\r\n");

      var result = [];
      var headers = lines[0].split(",");
      vm.parse_header = lines[0].split(",");
      lines[0].split(",").forEach(function(key) {
        vm.sortOrders[key] = 1;
      });
      lines.map(function(line, indexLine) {
        if (indexLine < 1) return;
        var obj = {};
        var currentline = line.split(",");

        headers.map(function(header, indexHeader) {
          obj[header] = currentline[indexHeader];
        });
        result.push(obj);
      });
      return result;
    },
    loadCSV(e) {
      var vm = this;
      if (window.FileReader) {
        const file = e.target.files[0];
        const reader = new FileReader();

        reader.onload = (e) => this.$emit("load", e.target.result);
        reader.readAsText(file);
        reader.onload = function(event) {
          var csv = event.target.result;
          vm.parse_csv = vm.csvJSON(csv);
        };
      } else {
        alert("FileReader are not supported in this browser.");
      }
    },
    compressToZip() {
      var zip = new JSZip();
      var vm = this;
      vm.parsed_footers.map(function(element) {
        zip.file(element.title, element.content);
      });
      zip.generateAsync({ type: "blob" }).then(function(content) {
        saveAs(content, "stopki2.zip");
      });
    },
    saveToFile(e) {
      var vm = this;
      if (vm.parse_csv) {
        vm.parse_csv.map(function(element) {
          var displayGA = "",
            displayIMG = "",
            singleFooter = {};
          if (element["CertyfikatGA"] === "Tak") {
            displayGA =
              '<img src="https://github.com/PatrykPuslecki/Footerator/blob/master/src/assets/ga.png?raw=true" class="gads" alt="" style="float: right; width: 150px;">';
          }
          if (element["Zdjęcie"]) {
            displayIMG =
              '<img src="' +
              element["Zdjęcie"] +
              '" class="photo" alt="" style="border-radius: 50%; height: 76px; float: left;">';
          }
          var footerContent =
            '<html> <head> <meta http-equiv="content-type" content="text/html; charset=UTF-8"><link href="https://fonts.googleapis.com/css?family=Muli:400,700,800,900&display=swap&subset=latin-ext" rel="stylesheet"> <title></title> </head> <body> <table style="color: rgb(0, 0, 0); font-family: Muli, Calibri, Tahoma; font-size: 10px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-style: initial; text-decoration-color: initial; width: 490pt; vertical-align: middle;" cellspacing="0" cellpadding="0" border="0"> <tbody> <tr> <td class="logo" style="padding: 5pt; width: 110pt;"></td><td class="middle" style="padding: 5pt; border-left: 3pt solid rgb(0, 158, 226); border-right: 3pt solid rgb(0, 158, 226); border-radius: 2pt; color: rgb(48, 50, 128);"> <table> <tbody> <tr> <td style="padding: 5pt;">' +
            displayIMG +
            '</td><td style="padding: 5pt; font-size: 10px;"> <p class="name" style="font-weight: bold; margin: 0px;">' +
            element["Imię"] +
            " " +
            element["Nazwisko"] +
            "</p>" +
            element["Stanowisko"] +
            '<br><br><a href="tel:' +
            element["Telefon"] +
            '" style="text-decoration: none; color: rgb(48, 50, 128);">' +
            element["Telefon"] +
            '</a><br><a href="mailto:' +
            element["Mail"] +
            '" style="text-decoration: none; color: rgb(48, 50, 128);">' +
            element["Mail"] +
            '</a></td><td style="padding: 5pt;">' +
            displayGA +
            '</td></tr></tbody> </table> </td><td class="small" style="padding: 5pt; font-size: 10px; color: rgb(48, 50, 128);">Patrico.io<br>tel: + 48 75650210<br>NIP: 11223344<br><br><a class="lightblue" href="https://test.pl" style="color: rgb(0, 158, 226); text-decoration: none;">Patrico.io</a></td></tr><tr> <td colspan="3"> <p style="background-color: #009EE2; padding-top: 3pt; border-radius: 3pt; margin: 0; margin-top: 10pt; margin-bottom: 10pt; font-size: 0;">&nbsp;</p></td></tr><tr> <td colspan="3" style="padding: 5pt;"></td></tr></tbody> </table> </body></html>';
          singleFooter.content = footerContent;
          singleFooter.title =
            element["Nazwisko"] + "_" + element["Imię"] + ".html";
          vm.parsed_footers.push(singleFooter);
          e.preventDefault();
        });
        vm.compressToZip();
      }
    },
  },
};
</script>
