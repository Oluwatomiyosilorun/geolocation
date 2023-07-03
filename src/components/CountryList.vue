<template>
  <div>
    <p>Country List</p>
    <button @click="getGeoLocation">Sort by Location</button>
    <CountryItem
      v-for="country in countries"
      :key="country.name"
      :country="country"
    />
  </div>
</template>

<script>
import CountryItem from "./CountryItem.vue";
import axios from "axios";

export default {
  components: {
    CountryItem,
  },
  data() {
    return {
      countries: [],
      userLocation: null,
    };
  },

  computed: {
    sortedCountries() {
      if (!this.userLocation) {
        return this.countries;
      }
      return this.countries
        .slice()
        .sort(
          (a, b) =>
            this.calculateDistance(a.latlng) - this.calculateDistance(b.latlng)
        );
    },
  },

  mounted() {
    axios
      .get("https://restcountries.com/v3.1/all?fields=name,flags,latlng")
      .then((response) => {
        const data = response.data;
        this.countries = data.map((country) => {
          const { flags, name, latlng } = country;
          return { flags, name, latlng };
        });

        console.log(this.countries, "data");
      })
      .catch((error) => {
        console.log(error);
      });
  },

  methods: {
    calculateDistance(latlng) {
      const userLat = this.userLocation.latitude;
      const userLng = this.userLocation.longitude;
      const countryLat = latlng[0];
      const countryLng = latlng[1];
      const earthRadius = 6371;
      const toRadians = (degrees) => {
        return (degrees * Math.PI) / 180;
      };
      const deltaLat = toRadians(countryLat - userLat);
      const deltaLng = toRadians(countryLng - userLng);
      const a =
        Math.sin(deltaLat / 2) * Math.sin(deltaLat / 2) +
        Math.cos(toRadians(userLat)) *
          Math.cos(toRadians(countryLat)) *
          Math.sin(deltaLng / 2) *
          Math.sin(deltaLng / 2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
      const distance = earthRadius * c;
      return distance;
    },

    sortCountriesByLocation() {
      if (this.userLocation) {
        this.countries = this.sortedCountries;
      } else {
        console.error('User location not available.');
      }
    },
    
    getGeoLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.userLocation = {
              latitude: position.coords.latitude,
              longitude: position.coords.longitude
            };
          },
          (error) => {
            console.error(error);
          }
        );
      } else {
        console.error('Geolocation is not supported by this browser.');
      }
    }
  },
};
</script>
