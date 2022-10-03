<template>
  <div class="tile" :style="style">
    <img :src="person.image" alt="">
    {{ person.name }} <br>
    {{ person.country }}
  </div>
</template>

<script>
import Person from "@/types/person";

export default {
  name: "ImageTile",
  props: {
    person: Person,
    numTiles: Number
  },
  computed: {
    style() {
      return {
        "width": "calc(" + (100 / this.numTiles).toString() + "% - 20px)"
      }
    }
  }
}
</script>

<style scoped>

  .div.tile {
    z-index: -1;
  }

  img {
    max-width: 100%;
    max-height: 100%;
  }
</style>
