<template>
  <div class="driver-view">
    <section class="top-bar">
      <div v-if="user">{{user.email}}</div>
      <div>Driver</div>
      <button class="ui button red" @click="signOutButtonPressed">Signout</button>
    </section>

    <section ref="map" class="map"></section>

    <section class="bottom-bar">
      <div class="latLngLabel">{{lat}}, {{lng}}</div>
      <button class="ui button green" @click="startLocationUpdates">
        <i class="circle dot outline icon large"></i>
        Start Location
      </button>

      <button class="ui button red" @click="stopLocationUpdates">
        <i class="circle dot outline icon large"></i>
        Stop Location
      </button>
    </section>
  </div>
</template>

<script>
import firebase from "firebase";
export default {
  data() {
    return {
      user: null,
      lat: 0,
      lng: 0,
      watchPositionId: null
    };
  },

  mounted() {
    firebase.auth().onAuthStateChanged(user => {
      this.user = user;
    });
  },

  methods: {
    startLocationUpdates() {
      var map = new google.maps.Map(this.$refs["map"], {
        zoom: 15,
        mapTypeId: google.maps.MapTypeId.ROADMAP
      });

      var marker = new google.maps.Marker({
        map: map
      });

      this.watchPositionId = navigator.geolocation.watchPosition(
        position => {
          this.lat = position.coords.latitude;
          this.lng = position.coords.longitude;

          map.setCenter(new google.maps.LatLng(this.lat, this.lng));
          marker.setPosition(new google.maps.LatLng(this.lat, this.lng));
          this.updateLocation(this.lat, this.lng);
        },
        error => {
          console.log(error.message);
        }
      );
    },

    stopLocationUpdates() {
      navigator.geolocation.clearWatch(this.watchPositionId);
    },

    signOutButtonPressed() {
      firebase
        .auth()
        .signOut()
        .then(user => {
          this.$router.push({
            name: "Signin"
          });
        })
        .catch(error => {
          console.log(error.message);
        });
    },
    updateLocation(lat, lng) {
      const db = firebase.firestore();
      db.collection("users")
        .doc(this.user.uid)
        .set({ lat: lat, lng: lng, active: true }, { merge: true });
    }
  }
};
</script>


<style>
.driver-view {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

.top-bar {
  height: 100px;
  text-align: center;
  background: white;
}

.map {
  flex-grow: 1;
  background-color: rgba(0, 0, 0, 0.1);
}

.bottom-bar {
  padding: 20px 0px;
  text-align: center;
  background: white;
}
</style>

