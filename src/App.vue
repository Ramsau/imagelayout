<template>
  <button @click="addEntry()">Add Entry</button>
  <button @click="startBulkImport()">Bulk import</button>
  <input type="file" multiple="true" ref="bulkFiles" @change="bulkImport($event)" class="hidden">
  <button @click="changeBgImage()">{{this.backgroundImage ? "Delete" : "Add"}} background</button>
  <button @click="printImage">Export image</button>
  <input type="file" ref="bgImageFile" @change="loadBgImage($event)" class="hidden">
  <a href="" ref="downloadLink" class="hidden" download="image.png"></a>
  <br>
  <label for="numTiles">Images per row: </label> <input type="number" id="numTiles" :value="numTiles" @change="numTiles = $event.target.value">

  <EditOverlay :person="editPerson" ref="editOverlay"
               @namechanged="editPerson.name = $event"
               @countrychanged="editPerson.country = $event"
               @imagechanged="changePersonImage($event)"
               @delete="deletePerson()"
  />
  <div class="frame">
    <ImageTile v-for="person of persons"
               :person="person"
               :numTiles="this.numTiles"
               :key="person" @click="changePerson(person)"/>
    <img :src="backgroundImage" alt="">
  </div>
  <div class="frame" id="exportFrame" ref="exportFrame" >
    <ImageTile v-for="person of persons"
               :person="person"
               :numTiles="this.numTiles"
               :key="person" @click="changePerson(person)"/>
    <img :src="backgroundImage" alt="">
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import Person from "@/types/person";
import EditOverlay from "@/components/EditOverlay.vue";
import ImageTile from "@/components/ImageTile.vue";

@Options({
  components: {ImageTile, EditOverlay},
  data() {
    return {
      persons: [],
      editPerson: undefined,
      numTiles: 4,
      backgroundImage: "",
    }
  },
  methods: {
    addEntry() {
      this.editPerson = new Person();
      this.persons.push(this.editPerson);
      this.$refs.editOverlay.open();
    },
    startBulkImport() {
      this.$refs.bulkFiles.click();
    },
    bulkImport(event: { target: { files: any; }; dataTransfer: { files: any; }; }) {
      var files = event.target.files || event.dataTransfer.files;
      if (!files.length) {
        return;
      }

      for (let file of files) {
        var reader = new FileReader();

        reader.onload = (e) => {
          var target = e ? e.target : null;
          var result = target ? target.result : null;
          this.editPerson = new Person();
          this.editPerson.image = result;
          this.persons.push(this.editPerson);
        }
        reader.readAsDataURL(file);
      }
    },
    changePerson(person: Person) {
      this.editPerson = person;
      this.$refs.editOverlay.open();
    },
    changePersonImage(event: { target: { files: any; }; dataTransfer: { files: any; }; }) {
      var files = event.target.files || event.dataTransfer.files;
      if (!files.length)
        return;
      var image = new Image();
      var reader = new FileReader();

      reader.onload = (e) => {
        var target = e ? e.target : null;
        var result = target ? target.result : null;
        this.editPerson.image = result;
      };
      reader.readAsDataURL(files[0]);
    },
    deletePerson() {
      this.$refs.editOverlay.close();
      this.persons = this.persons.filter((p: Person) => p !== this.editPerson);
      this.editPerson = this.persons[0];
    },
    changeBgImage() {
      if (this.backgroundImage === "") {
        this.$refs.bgImageFile.click();

      } else {
        this.backgroundImage = "";
      }
    },
    loadBgImage(event: { target: { files: any; }; dataTransfer: { files: any; }; }) {
      var files = event.target.files || event.dataTransfer.files;
      if (!files.length)
        return;
      var reader = new FileReader();

      reader.onload = (e) => {
        var target = e ? e.target : null;
        var result = target ? target.result : null;
        this.backgroundImage = result;
      };
      reader.readAsDataURL(files[0]);
    },
    printImage() {
      // eslint-disable-next-line @typescript-eslint/ban-ts-comment
      // @ts-ignore
      // eslint-disable-next-line no-undef
      html2canvas(this.$refs.exportFrame).then(el => {
        var imageData = el.toDataURL("image/png");
        var dlData = imageData.replace(/^data:image\/png/, "data:application/octet-stream");
        this.$refs.downloadLink.href=dlData;
        this.$refs.downloadLink.click();
      });
    }
  },
})
export default class App extends Vue {}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.frame  {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  gap: 20px;
  padding-top: 20px;
  overflow: hidden;
}

.frame#exportFrame {
  position: fixed;
  left: 20000px;
  top: -500px;
  min-height: 1080px;
  width: 1920px;
}

.frame > img {
  position: absolute;
  z-index: -1;
  margin-top: -20px;
}

input.hidden {
  display: none;
}
</style>
