<template>
  <div id="container" class="container">
    <div class="header header--big">Dodaj lek</div>
    <div class="container__form">
      <div class="container__information">
        <div class="element">
          <label for="drugTitle" class="container__title">
            Nazwa leku:
          </label>
          <div>
            <select
              name="drugTitle"
              id="drugTitle"
              v-model="drugTitle"
              form="dataForm"
              class="input"
            >
              <option v-for="element of medicines" :key="element.id">{{
                element.name
              }}</option>
            </select>
          </div>
        </div>
        <div class="element element__hours">
          <label class="container__title">Godzina: </label>
          <div>
            <label for="drugMorning">8:00</label>
            <input
              type="checkbox"
              name="drugMorging"
              value="8"
              v-model="drugHour"
              class="input--checkbox"
            />
            <label for="drugAfternoon">15:00</label>
            <input
              type="checkbox"
              name="drugAfternoon"
              value="15"
              v-model="drugHour"
              class="input--checkbox"
            />
            <label for="drugEvening">22:00</label>
            <input
              type="checkbox"
              name="drugEvening"
              value="22"
              v-model="drugHour"
              class="input--checkbox"
            />
          </div>
        </div>
        <div class="element">
          <label for="drugDate" class="container__title">Data: </label>
          <div>
            <input
              type="date"
              name="drugDate"
              v-model="drugDate"
              class="input"
            />
          </div>
        </div>
        <div class="element">
          <label for="drugAmount" class="container__title">Ilość: </label>
          <div>
            <input
              name="drugAmount"
              type="number"
              min="0"
              v-model="drugAmount"
              class="input"
            />
          </div>
        </div>
        <div class="element">
          <label for="ward" class="container__title">Oddział: </label>
          <div>
            <select
              name="ward"
              id="ward"
              v-model="ward"
              form="dataForm"
              class="input"
            >
              <option v-for="element of wards" :key="element.id">{{
                element.name
              }}</option>
            </select>
          </div>
        </div>
      </div>
      <div class="center header header--medium">Pacjent</div>
      <div class="container__patient">
        <div class="element">
          <label for="personName" class="container__title">Imię: </label>
          <div>
            <input
              type="text"
              name="personName"
              v-model="personName"
              class="input"
            />
          </div>
        </div>
        <div class="element">
          <label for="personSurname" class="container__title">Nazwisko: </label>
          <div>
            <input
              type="text"
              name="personSurname"
              v-model="personSurname"
              class="input"
            />
          </div>
        </div>
        <div class="element">
          <label for="personId" class="container__title">Pesel: </label>
          <div>
            <input
              type="text"
              name="personId"
              v-model="personId"
              class="input"
            />
          </div>
        </div>
      </div>
    </div>
    <button v-on:click="isValid" class="container__button">Dodaj</button>
    <div class="error">{{ error }}</div>
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
const baseURL = "http://localhost:3000/person";
const medicineURL = "http://localhost:3000/medicine";
const wardURL = "http://localhost:3000/wards";

import Card from "./Card";

export default {
  name: "Container",
  components: {
    Card
  },
  data() {
    return {
      planner: [],
      medicines: [],
      wards: [],
      drugTitle: "",
      drugHour: [],
      drugDate: "",
      drugAmount: "",
      ward: "",
      personName: "",
      personSurname: "",
      personId: "",
      error: ""
    };
  },

  async created() {
    try {
      const res = await axios.get(baseURL);
      const resMedicine = await axios.get(medicineURL);
      const resWards = await axios.get(wardURL);

      this.planner = res.data;
      this.medicines = resMedicine.data;
      this.wards = resWards.data;
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
      const res = await axios.delete(baseURL + "/" + id);
      this.planner = [...this.planner, res.data];
      window.location.reload();
    },
    isFormFilled() {
      return (
        this.drugTitle &&
        this.drugHour.length > 0 &&
        this.drugDate &&
        this.drugAmount &&
        this.ward &&
        this.personName &&
        this.personSurname &&
        this.personId
      );
    },
    isPeselValid(pesel) {
      const weight = [1, 3, 7, 9, 1, 3, 7, 9, 1, 3];
      let sum = 0;
      let controlNumber = parseInt(pesel.substring(10, 11));
      for (let i = 0; i < weight.length; i++) {
        sum += parseInt(pesel.substring(i, i + 1)) * weight[i];
      }
      sum = sum % 10;
      return (10 - sum) % 10 === controlNumber;
    },
    sortHour() {
      this.drugHour = this.drugHour.map(function(x) {
        return parseInt(x, 10);
      });
      this.drugHour = this.drugHour.sort((a, b) => a - b);
    },
    isValid() {
      this.error = "";
      this.sortHour();
      if (this.isFormFilled()) {
        if (this.isPeselValid(this.personId)) {
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
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 70%;
    margin: 20px 0;
    padding: 20px 0;
    background-color: rgba(255, 255, 255, 0.7);
    border-radius: 10px;

    @media screen and (max-width: 1024px) {
      width: 90%;
    }
  }
  &__information {
    display: grid;
    grid-template-areas: "a a a a a";
    grid-template-rows: 1fr;
    column-gap: 20px;
    row-gap: 20px;
    justify-items: center;

    @media screen and (max-width: 1280px) {
      grid-template-areas: "a a a";
    }

    @media screen and (max-width: 768px) {
      grid-template-areas: "a a";
    }
    @media screen and (max-width: 600px) {
      grid-template-areas: "a";
    }
  }
  &__patient {
    display: grid;
    grid-template-areas: "a a a";
    grid-template-rows: 1fr;
    column-gap: 20px;
    row-gap: 20px;
    justify-items: center;

    @media screen and (max-width: 1024px) {
      grid-template-areas: "a a";
    }
    @media screen and (max-width: 600px) {
      grid-template-areas: "a";
    }
  }
  &__title {
    font-weight: 600;
  }
  &__button {
    font-size: 1.2em;
    margin-bottom: 20px;
    padding: 10px 15px;
    border: none;
    border-radius: 30px;
    color: #fff;
    background-color: #154fb5;
    outline: none;
    cursor: pointer;
    transition: 0.2s ease-in background-color;
    &:hover {
      background-color: #1960dc;
    }
  }
  &__card {
    display: grid;
    grid-template-areas: "a a a a";
    grid-template-rows: 1fr;
    column-gap: 25px;
    justify-items: center;

    @media screen and (max-width: 1024px) {
      grid-template-areas: "a a a";
    }

    @media screen and (max-width: 768px) {
      grid-template-areas: "a a";
    }

    @media screen and (max-width: 480px) {
      grid-template-areas: "a";
    }
  }
}

.element {
  &__hours {
    display: inline-block;
  }
  @media screen and (max-width: 1280px) {
    margin: 0px 10px;
  }
}
.center {
  text-align: center;
}

.header {
  margin: 10px 0;
  font-weight: 600;

  &--big {
    font-size: 2em;
  }

  &--medium {
    font-size: 1.5em;
    margin: 10px 0;
    @media screen and (max-width: 600px) {
      margin: 30px 0;
    }
  }
}
.input {
  width: 175px;
  height: 25px;
  background-color: transparent;
  border: none;
  border-bottom: 2px solid #154fb5;
  outline: none;

  &--checkbox {
    margin: 0 10px;
  }
}
.error {
  color: red;
}
</style>
