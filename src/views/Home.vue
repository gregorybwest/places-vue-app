<script>
import axios from "axios";

export default {
  data: function () {
    return {
      places: [],
      newPlaceParams: {},
      currentPlace: {},
      editPlaceParams: {},
    };
  },
  created: function () {
    this.placesIndex();
  },
  methods: {
    placesIndex: function () {
      axios.get("/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    placesCreate: function () {
      axios
        .post("/places", this.newPlaceParams)
        .then((response) => {
          console.log("Success!", response.data);
          this.places.push(response.data);
          this.newPlaceParams = {};
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    placesShow: function (place) {
      console.log(place);
      this.currentPlace = place;
      this.editPlaceParams = place;
      document.querySelector("#place-details").showModal();
    },
    placesUpdate: function (place) {
      axios
        .patch(`/places/${place.id}`, place)
        .then((response) => {
          console.log("Success", response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    placesDestroy: function (place) {
      axios.delete(`/places/${place.id}`).then((response) => {
        console.log("Successfully deleted", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>

<template>
  <div class="home">
    {{ newPlaceParams }}
    <div>
      Name:
      <input type="text" v-model="newPlaceParams.name" />
      <br />
      Address:
      <input type="text" v-model="newPlaceParams.address" />
      <br />
      <button v-on:click="placesCreate()">Create</button>
    </div>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>{{ place.name }}</h2>
      <button v-on:click="placesShow(place)">more info:</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h2>{{ currentPlace.address }}</h2>
        <p>
          Name:
          <input type="text" v-model="editPlaceParams.name" />
        </p>
        <p>
          Adress:
          <input type="text" v-model="editPlaceParams.address" />
        </p>
        <button v-on:click="placesUpdate(currentPlace)">Update</button>
        <button v-on:click="placesDestroy(currentPlace)">Delete</button>
        <button>close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>
