<template>
  <div class="card__wrapper">
    <div>Nazwa: {{ item.name }}</div>
    <div>
      Godziny:
      <ul>
        <li v-for="i in hours" :key="i">{{ i }}:00</li>
      </ul>
    </div>
    <div>Data: {{ item.date }}</div>
    <div>Ilość: {{ item.amount }}</div>
    <div>Oddział: {{ item.ward }}</div>
    <div>Imię: {{ item.person.name }}</div>
    <div>Nazwisko: {{ item.person.surname }}</div>
    <div>Pesel: {{ item.person.pesel }}</div>
    <div>ID: {{ item.id }}</div>
    <button @click="deleteItem(item.id)">USUŃ</button>
  </div>
</template>
<script>
export default {
  name: "Card",
  props: ["item", "hours"],
  methods: {
    deleteItem(id) {
      this.$emit("clicked", id);
    }
  }
};
</script>
<style lang="scss">
.card {
  &__wrapper {
    width: 80%;
    margin: 20px;
    padding: 15px;
    border: 2px solid gray;
    border-radius: 10px;
  }
}
</style>
