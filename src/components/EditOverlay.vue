<template>
  <div id="editBackdrop" @click="close()" v-if="isOpen">
    <div id="editOverlay" @click="$event.stopPropagation()">
      <template v-if="person">
        <input type="text" :value="person.name" @change="$emit('namechanged', $event.target.value)" placeholder="Name">
        <input type="text" :value="person.country" @change="$emit('countrychanged', $event.target.value)" placeholder="Land">
        <input type="file" @change="$emit('imagechanged', $event)">
      </template>
    </div>
  </div>
</template>

<script>
import Person from "@/types/person";

export default {
  name: "EditOverlay",
  data() {
    return {
      isOpen: false,
    }
  },
  props: {
    person: Person
  },
  methods: {
    open() {
      this.isOpen = true;
    },
    close() {
      this.isOpen = false;
    },
    onFileChange(item, e) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      this.createImage(item, files[0]);
    },
    createImage(item, file) {
      var image = new Image();
      var reader = new FileReader();

      reader.onload = (e) => {
        item.image = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    removeImage: function (item) {
      item.image = false;
    }
  }
}
</script>

<style scoped>
  #editBackdrop {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(82, 68, 68, 0.5);
  }

  #editOverlay {
    position: fixed;
    top: 2em;
    left: 4em;
    right: 4em;
    bottom: 2em;
    background: white;
    border-radius: 1em;
  }
</style>
