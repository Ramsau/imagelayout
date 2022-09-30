<template>
  <div class="tile">
    {{ person.name }} <br>
    {{ person.country }}
    <img :src="person.image" alt="">
  </div>
</template>

<script>
import Person from "@/types/person";

export default {
  name: "ImageTile",
  props: {
    person: Person,
  }
}
</script>

<style scoped>
  .tile {
    background-color: #939393;
    width: 25%;
  }
</style>
