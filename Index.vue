<template>
  <q-page class="flex column" :class="bgClass">
    
    <div class="col q-pt-lg q-px-md">
      <q-input
        v-model="search"
        @keyup.enter="getNamazBySearch"
        placeholder="Search"
        dark
        borderless
      >
        <template v-slot:before>
          <q-icon @click="getLocation" name="my_location" />
        </template>

        <template v-slot:append>
          <q-btn @click="getNamazBySearch" round dense flat icon="search" />
        </template>
      </q-input>
    </div>

    <template v-if="namazData">
      <div class="  text-white text-center">
        <h1 class="text-h2 text-weight-light">
          {{ weatherData.name }}
        </h1>
      </div>

      <div class="col text-white text-center">
        <table
          class="col text-white text-center"
          style="border-collapse: collapse; width: 100%;"
        >
          <tbody>
            <tr class="text-h4 text-weight-light" style="height: 20px;">
              <td style="width: 50%; height: 20px;">Fajr</td>
              <td style="width: 50%; height: 20px;">{{ namazData.data.timings.Fajr }}</td>
            </tr>
            <tr class="text-h4 text-weight-light" style="height: 20px;">
              <td style="width: 50%; height: 20px;">Zuhr</td>
              <td style="width: 50%; height: 20px;">{{ namazData.data.timings.Dhuhr }}</td>
            </tr>
            <tr class="text-h4 text-weight-light" style="height: 20px;">
              <td style="width: 50%; height: 20px;">Asr</td>
              <td style="width: 50%; height: 20px;">{{ namazData.data.timings.Asr }}</td>
            </tr>
            <tr class="text-h4 text-weight-light" style="height: 15px;">
              <td style="width: 50%; height: 15px;">Maghrib</td>
              <td style="width: 50%; height: 15px;">{{ namazData.data.timings.Maghrib }}</td>
            </tr>
            <tr class="text-h4 text-weight-light" style="height: 15px;">
              <td style="width: 50%; height: 15px;">Esha</td>
              <td style="width: 50%; height: 15px;">{{ namazData.data.timings.Isha }}</td>
            </tr>
          </tbody>
        </table>
        <p></p>
      </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">Salah <br />Times</div>
      </div>

      <q-btn @click="getLocation" color="white" class="col" flat>
        <q-icon left size="3em" name="my_location" />
        <div>Find my location</div>
      </q-btn>
    </template>
  <div class="col skyline">
  </div>
  </q-page>

</template>


<script>
export default {
  name: "PageIndex",
  data() {
    return {
      search: "",
      namazData: null,
      weatherData: null,
      lat: null,
      long: null,
      api: "b1e6aa1735fe02c21d6feeeeaacda497",
      namazurl: "http://api.aladhan.com/v1/",
      url: "https://api.openweathermap.org/data/2.5/weather"
    };
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith("n")) {
          return "bg-night";
        } else {
          return "bg-day";
        }
      }
    }
  },

  methods: {
    getLocation() {
      this.$q.loading.show();
      navigator.geolocation.getCurrentPosition(position => {
        console.log("position: ", position);
        this.lat = position.coords.latitude;
        this.long = position.coords.longitude;
        this.getNamazByCoords();
      });
    },

    getNamazByCoords() {
      this.$q.loading.show();
      this.$axios(
        `${this.namazurl}timings/date_or_timestamp?latitude=${this.lat}&longitude=${this.long}`
      ).then(response => {
        this.namazData = response.data;
        this.getWeatherByCoords();
        this.$q.loading.hide();
      });
    },

    getNamazBySearch() {
      this.$q.loading.show();
      this.$axios(
        `${this.namazurl}timingsByCity?city=${this.search}&country=UK`
      ).then(response => {
        this.namazData = response.data;
        this.getWeatherBySearch();
        this.$q.loading.hide();
      });
    },

    getWeatherByCoords() {
      this.$q.loading.show();
      this.$axios(
        `${this.url}?lat=${this.lat}&lon=${this.long}&appid=${this.api}&units=metric`
      ).then(response => {
        console.log("response", response);
        this.weatherData = response.data;
        this.$q.loading.hide();
      });
    },

    getWeatherBySearch() {
      this.$q.loading.show();
      this.$axios(`${this.url}?q=${this.search}&appid=${this.api}`).then(
        response => {
          console.log("resp", response);
          this.weatherData = response.data;
          this.$q.loading.hide();
        }
      );
    }
  }
};
</script>
<style lang="sass">
.q-page
  background: linear-gradient(to bottom, #136a8a, #267871)
  &.bg-night
    background: linear-gradient(to bottom, #232526, #414345)
  &.bg-day
    background: linear-gradient(to bottom, #00b4db, #0083b0)
.degree
  top: -44px

.skyline
  flex: 0 0 15qu0px
  background: url(../assets/skyline.png)
  background-size: contain
  background-position: center bottom
</style>
