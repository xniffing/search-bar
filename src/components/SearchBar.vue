<template>
  <div class="form-search">
    <div class="date-picker">
      <div class="title">TRAVEL PERIOD</div>
      <VueDatePicker
        v-model="data"
        auto-apply
        multi-calendars
        range
        :enable-time-picker="false"
        :format="format"
      />
    </div>
    <div class="separator"></div>
    <div class="search-form">
      <div class="title">LOCATION</div>
      <select
        name="city"
        v-model="localization"
        class="form-control select"
        aria-label="City"
        id="localizacao"
      >
        <option value="" selected="">City</option>
        <option value="alcobaça">Alcobaça</option>
        <option value="bombarral">Bombarral</option>
        <option value="caldas da rainha">Caldas da Rainha</option>
        <option value="lourinhã">Lourinhã</option>
        <option value="nazaré">Nazaré</option>
        <option value="peniche">Peniche</option>
        <option value="porto de mós">Porto de Mós</option>
        <option value="rio maior">Rio Maior</option>
        <option value="silves">Silves</option>
        <option value="óbidos">Óbidos</option>
      </select>
    </div>
    <div class="separator"></div>
    <div class="guests">
      <div class="title">GUESTS</div>
      <div class="guest-menu">
        <div @click="visible = !visible">
          {{ adults + children }}
          {{ adults + children === 1 ? "guest" : "guests" }}
        </div>
        <div class="caret" @click="visible = !visible" v-if="!visible">
          <b>&or;</b>
        </div>
        <div class="caret" @click="visible = !visible" v-if="visible">
          <b>&and;</b>
        </div>
      </div>

      <div class="wrapper" v-if="visible">
        <div class="adults">
          <div>Adults</div>
          <button @click="decrementAdults">-</button>
          <input
            type="integer"
            v-model="adults"
            min="1"
            max="10"
            placeholder="1"
          />
          <button @click="incrementAdults">+</button>
        </div>
        <div class="children">
          <div>Children</div>
          <button @click="decrementChilds">-</button>
          <input
            type="integer"
            v-model="children"
            min="0"
            max="10"
            placeholder="0"
          />
          <button @click="incrementChilds">+</button>
        </div>
      </div>
      <div class="backdrop" v-if="visible" @click="visible = !visible"></div>
    </div>
    <div class="search-button">
      <button @click="redirect" class="button is-link is-rounded">
        Search
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, watch } from "vue";
import VueDatePicker from "@vuepic/vue-datepicker";
import "@vuepic/vue-datepicker/dist/main.css";

const visible = ref(false);

const data = ref();

const localization = ref("");

const adults = ref(1);

const children = ref(0);

const startDate = computed(() => {
  if (data.value && data.value.length > 0) {
    const start = data.value[0];
    const formattedStart = formatDate(start);
    return formattedStart;
  }
  return "";
});

const incrementAdults = () => {
  if (adults.value >= 1) {
    adults.value++;
  } else {
    adults.value = 1;
  }
};

const decrementAdults = () => {
  if (adults.value > 2) {
    adults.value--;
  } else {
    adults.value = 1;
  }
};

const incrementChilds = () => {
  if (children.value >= 0) {
    children.value++;
  } else {
    children.value = 0;
  }
};

const decrementChilds = () => {
  if (children.value > 1) {
    children.value--;
  } else {
    children.value = 0;
  }
};

const endDate = computed(() => {
  if (data.value && data.value.length > 1) {
    const end = data.value[1];
    const formattedEnd = formatDate(end);
    return formattedEnd;
  }
  return "";
});

function formatDate(date) {
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, "0");
  const day = String(date.getDate()).padStart(2, "0");
  return `${year}-${month}-${day}`;
}

function redirect() {
  const encodedLocalization = encodeURIComponent(localization.value);
  const encodedStartDate = encodeURIComponent(startDate.value);
  const encodedEndDate = encodeURIComponent(endDate.value);
  const url = `https://bookings.feathershouses.com/en-GB/rentals/?checkinDate=${encodedStartDate}&checkoutDate=${encodedEndDate}&city=${encodedLocalization}&adults=${adults.value}&children=${children.value}`;
  window.location.href = url;
}

const format = "yyyy/MM/dd - yyyy/MM/dd";
</script>

<style scoped>
.form-search {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 3rem;
  position: relative;
  background-color: rgb(255, 255, 255);
  box-shadow: rgba(0, 0, 0, 0.07) 0px 3px 7px 0px;
  padding: 15px 30px;
  z-index: 1;
}

.search-form select {
  background-color: white;
  border: none;
  padding: 0.5em;
  border-radius: 0.2em;
  margin: 0;
  width: 100%;
  font-family: inherit;
  font-size: inherit;
  cursor: inherit;
  line-height: inherit;
}

.adults > div,
.children > div {
  flex: 1;
}

.adults input,
.children input {
  width: 1.2em;
  border: none;
  height: 2em;
  padding: 0.5em;
  border-radius: 5px;
  text-align: center;
}

.guests {
  position: relative;
  cursor: pointer;
  background: white;
  padding: 0.5rem;
  border-radius: 0.2rem;
}

.wrapper {
  flex-direction: column;
  padding: 1em;
  position: absolute;
  margin-top: 0.5em;
  background: white;
  min-width: 11em;
  border-radius: 1rem;
  display: flex;
  align-content: center;
  justify-content: space-evenly;
  margin-left: calc(50% - 6em);
  z-index: 10;

  & > div {
    display: flex;
    justify-content: space-between;
    padding: 0.5em;
    align-items: center;
  }
}

.guest-menu {
  display: flex;
  gap: 0.5em;
}

button {
  background: white;
  border: 1px solid #ccc;
  border-radius: 2rem;
  cursor: pointer;
  height: 2em;
  width: 2em;
  transform: scale(0.8);
}

.backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  background: rgba(0, 0, 0, 0);
}

.title {
  font-size: 0.8em;
  color: #666;
  margin-bottom: 0.5em;
}

.button.is-link {
  height: 60px;
  width: 100%;
}

.button.is-rounded {
  border-radius: 9999px;
  padding-left: 1.25em;
  padding-right: 1.25em;
}
.button.is-link {
  background-color: #485fc7;
  border-color: transparent;
  color: #fff;
}

.button {
  font-family: Roboto, sans-serif;
  font-size: 1.2rem;
}
.button {
  background-color: #fff;
  border-color: #dbdbdb;
  border-width: 1px;
  color: #363636;
  cursor: pointer;
  justify-content: center;
  padding: calc(0.5em - 1px) 1em;
  text-align: center;
  white-space: nowrap;
}

.separator {
  width: 1px;
  height: 50px;
  background-color: #ccc;
  margin: 0 20px;
}
</style>
