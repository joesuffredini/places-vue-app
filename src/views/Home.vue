<template>
  <div class="places-vue-app">
    <h1>The Places App</h1>

    <!-- !-- Create Button -->
    <div>
      <h2>Create New Places for the Database</h2>
      Name:
      <input type="text" v-model="newPlaceName" />
      <br />
      Address:
      <input type="text" v-model="newPlaceAddress" />
      <br />
      <button v-on:click="createPlace()">Create</button>
    </div>

    <div>
      <div v-for="place in places" v-bind:key="place.id">
        <p>{{ place.name }}</p>
        <p>{{ place.address }}</p>
        <button v-on:click="showPlace(place)">Click for more info</button>
      </div>
    </div>

    <!-- Modal  -->

    <dialog id="place-details">
      <form method="dialog">
        <h1>Name info</h1>
        <p>
          Name:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <br />
        <p>
          Address:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <p>
          <br />
          <button v-on:click="updatePlace(currentPlace)">Update</button>
          <button v-on:click="deletePlace(currentPlace)">Delete</button>
          <button>Exit</button>
        </p>
      </form>
    </dialog>
  </div>
</template>

<style></style>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      places: [],
      newPlaceName: [],
      newPlaceAddress: [],
      currentPlace: [],
    };
  },

  created: function () {
    this.indexPlaces();
  },

  methods: {
    indexPlaces: function () {
      axios.get("/api/places").then((response) => {
        this.place = response.data;
        console.log(this.place);
      });
    },
    createPlace: function () {
      console.log("Create Place");
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("/api/places", params)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address,
      };
      axios.patch("/api/places/" + place.id, params).then((response) => {
        console.log("Success", response.data);
      });
    },
    deletePlace: function (place) {
      axios.delete("/api/places/" + place.id).then((response) => {
        console.log("Success!", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
