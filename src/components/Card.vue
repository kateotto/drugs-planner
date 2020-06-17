<template>
  <div class="card__wrapper">
    <div><span class="card__title">Nazwa leku:</span> {{ item.name }}</div>
    <div>
      <span class="card__title">Godziny:</span>
      <ul>
        <li v-for="i in hours" :key="i">{{ i }}:00</li>
      </ul>
    </div>
    <div><span class="card__title">Data:</span> {{ item.date }}</div>
    <div><span class="card__title">Ilość:</span> {{ item.amount }}</div>
    <div><span class="card__title">Oddział:</span> {{ item.ward }}</div>
    <div class="card__title">Pacjent:</div>
    <div><span class="card__title">Imię:</span> {{ item.person.name }}</div>
    <div>
      <span class="card__title">Nazwisko:</span> {{ item.person.surname }}
    </div>
    <div><span class="card__title">Pesel:</span> {{ item.person.pesel }}</div>
    <button @click="deleteItem(item.id)" class="card__button">&times;</button>
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
  position: relative;

  &__wrapper {
    width: 80%;
    margin: 20px;
    padding: 15px;
    background-color: rgba(255, 255, 255, 0.3);
    box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.4);
    border: 2.5px solid #154fb5;
    border-radius: 10px;
    position: relative;
  }
  &__title {
    font-weight: 600;
    color: #0d3e92;
  }
  &__button {
    position: absolute;
    top: 7px;
    right: 7px;
    font-size: 1.75em;
    border: none;
    background-color: transparent;
    cursor: pointer;
    outline: none;
    transition: 0.3s ease-in color;
    &:hover {
      color: #1449a9;
    }
  }
}
</style>
