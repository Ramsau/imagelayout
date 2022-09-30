<template>
  <button @click="addEntry()">Add Entry</button>
  <EditOverlay :person="editPerson" ref="editOverlay"
               @namechanged="editPerson.name = $event"
               @countrychanged="editPerson.country = $event"
               @imagechanged="changePersonImage($event)"
  />
  <div id="frame">
    <ImageTile v-for="person of persons" :person="person" :key="person" @click="changePerson(person)"/>
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
    }
  },
  methods: {
    addEntry() {
      this.editPerson = new Person();
      this.persons.push(this.editPerson);
      this.$refs.editOverlay.open();
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
        console.log( result);
        this.editPerson.image = result;
      };
      reader.readAsDataURL(files[0]);
    }
  }
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
  margin-top: 60px;
}

#frame  {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  gap: 20px;
}
</style>
