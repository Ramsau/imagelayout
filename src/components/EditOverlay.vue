<template>
  <div id="editBackdrop" @click="close()" :class="{closed: !isOpen}">
    <div id="editOverlay" @click="$event.stopPropagation()" @keyup.enter="close()" @keyup.esc="close()" v-if="person">
        <input type="text" :value="person.name" @change="$emit('namechanged', $event.target.value)" placeholder="Name" ref="nameinput">
        <br>
        <input type="text" :value="person.country" @change="$emit('countrychanged', $event.target.value)" placeholder="Land">
        <br>
        <input type="file" @change="$emit('imagechanged', $event)"> <br>
      <button @click="close">Close</button>
      <button @click="$emit('delete')">Delete</button>
      <br>
        <img :src="person.image" alt="">
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
      window.setTimeout(() => this.$refs.nameinput.focus(), 0);
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

  #editBackdrop.closed {
    display: none;
  }

  #editOverlay {
    position: fixed;
    top: 2em;
    left: 4em;
    right: 4em;
    bottom: 2em;
    background: white;
    border-radius: 1em;
    padding: 2em;
    z-index: 1;
  }

  img {
    max-width: calc(100%);
    max-height: 500px;
    margin-top: 3em;
  }
</style>
