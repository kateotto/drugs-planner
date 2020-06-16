<template>
  <div id="container" class="container">
    <h1>Dodaj leki</h1>
    <div class="container__form">
      <div class="container__drugInformation">
        <div class="element">
          <label for="drugTitle">Nazwa leku: </label>
          <select
            name="drugTitle"
            id="drugTitle"
            v-model="drugTitle"
            form="dataForm"
          >
            <option value="Polocard">Polocard</option>
            <option value="Ketonal">Ketonal</option>
            <option value="Heviran">Heviran</option>
            <option value="Aspiran">Aspiran</option>
          </select>
        </div>
        <div class="container__drugInformation element">
          <label for="drugMorning">Godzina: </label>
          <label for="drugMorning">8:00</label>
          <input
            type="checkbox"
            name="drugMorging"
            value="8"
            v-model="drugHour"
          />
          <label for="drugAfternoon">15:00</label>
          <input
            type="checkbox"
            name="drugAfternoon"
            value="15"
            v-model="drugHour"
          />
          <label for="drugEvening">22:00</label>
          <input
            type="checkbox"
            name="drugEvening"
            value="22"
            v-model="drugHour"
          />
        </div>
        <div class="element">
          <label for="drugDate">Data: </label>
          <input type="date" name="drugDate" v-model="drugDate" />
        </div>
        <div class="element">
          <label for="drugAmount">Ilość: </label>
          <input name="drugAmount" type="number" min="0" v-model="drugAmount" />
        </div>
        <div class="element">
          <label for="ward">Oddział: </label>
          <select name="ward" id="ward" v-model="ward" form="dataForm">
            <option value="kardiologia">Kardiologia</option>
            <option value="okulistyka">Okulistyka</option>
            <option value="chirurgia">Chirurgia</option>
          </select>
        </div>
      </div>
      <h3 class="center">Pacjent</h3>
      <div class="container__drugPatient">
        <div class="element">
          <label for="personName">Imię: </label>
          <input type="text" name="personName" v-model="personName" />
        </div>
        <div class="element">
          <label for="personSurname">Nazwisko: </label>
          <input type="text" name="personSurname" v-model="personSurname" />
        </div>
        <div class="element">
          <label for="personId">Pesel: </label>
          <input type="text" name="personId" v-model="personId" />
        </div>
      </div>
    </div>
    <button v-on:click="isValidate" class="container__button">Dodaj</button>
    <div>{{ error }}</div>
    <div class="container__card">
      <Card
        v-for="item of planner"
        :key="item.id"
        :item="item"
        :hours="item.hour"
        @clicked="deleteItem"
      />
    </div>
  </div>
</template>
<script>
import axios from "axios";
const baseURL = "http://localhost:3000/drugs";

import Card from "./Card";

export default {
  name: "Container",
  components: {
    Card
  },
  data() {
    return {
      planner: [],
      drugTitle: "",
      drugHour: [],
      drugDate: "",
      drugAmount: "",
      ward: "",
      personName: "",
      personSurname: "",
      personId: "",
      isPesel: "",
      error: ""
    };
  },

  async created() {
    try {
      const res = await axios.get(baseURL);

      this.planner = res.data;
    } catch (e) {
      console.error(e);
    }
  },
  methods: {
    resetData() {
      this.drugTitle = "";
      this.drugHour = [];
      this.drugMorning = "";
      this.drugAfternoon = "";
      this.drugEvening = "";
      this.drugDate = "";
      this.drugAmount = "";
      this.ward = "";
      this.personName = "";
      this.personSurname = "";
      this.personId = "";
      this.isPesel = "";
    },
    async addItem() {
      const res = await axios.post(baseURL, {
        name: this.drugTitle,
        hour: this.drugHour,
        date: this.drugDate,
        amount: this.drugAmount,
        ward: this.ward,
        person: {
          name: this.personName,
          surname: this.personSurname,
          pesel: this.personId
        }
      });

      this.planner = [...this.planner, res.data];

      this.resetData();
    },
    async deleteItem(id) {
      console.log(id);
      const res = await axios.delete(baseURL + "/" + id);
      this.planner = [...this.planner, res.data];
      window.location.reload();
    },
    isPeselValidate(pesel) {
      let weight = [1, 3, 7, 9, 1, 3, 7, 9, 1, 3];
      let sum = 0;
      let controlNumber = parseInt(pesel.substring(10, 11));
      for (let i = 0; i < weight.length; i++) {
        sum += parseInt(pesel.substring(i, i + 1)) * weight[i];
      }
      sum = sum % 10;
      this.isPesel = (10 - sum) % 10 === controlNumber;
    },
    isValidate() {
      this.error = "";
      this.drugHour = this.drugHour.map(function(x) {
        return parseInt(x, 10);
      });
      this.drugHour = this.drugHour.sort((a, b) => a - b);
      this.isPeselValidate(this.personId);
      if (
        this.drugTitle &&
        this.drugHour.length > 0 &&
        this.drugDate &&
        this.drugAmount &&
        this.ward &&
        this.personName &&
        this.personSurname &&
        this.personId
      ) {
        if (this.isPesel) {
          this.addItem();
        } else {
          this.error = "Pesel niepoprawny";
        }
      } else {
        this.error = "Wypełnij wszystkie pola";
      }
    }
  }
};
</script>
<style lang="scss">
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  &__form {
    width: 60%;
  }
  &__drug {
    &Information {
      display: flex;
      justify-content: space-around;
    }
    &Patient {
      display: flex;
      justify-content: space-evenly;
    }
  }
  &__button {
    margin: 10px 0;
  }
  &__card {
    display: grid;
    grid-template-areas: "a a a a";
    grid-template-rows: 1fr;
    justify-items: center;
  }
}
.center {
  text-align: center;
}
</style>
