<template>
  <div>
    <v-progress-linear v-bind:indeterminate="true" class="main" v-show="loading"></v-progress-linear>
    <div v-show="error.is" class="main">
      <h2>{{error.whattosay}}</h2>
    </div>
    <div v-show="!loading && !error.is">
      <div>


        <v-container fluid grid-list-lg>
  <v-layout row wrap>

    <v-flex xs12 sm12 md6 lg4 xl3  v-for="(hotel, i) in hotels" :key="i">
      <v-card flat tile color="primary">
        <v-card-media :src="hotel.image" height="300px"></v-card-media>
        <!-- <img :src="hotel.image" style="height:350px; max-weidth:100%;"> -->
        <v-card-title primary-title style="color:white">
          <div>
            <h3 class="htl-name mb-0">{{hotel.name}}</h3>

          </div>
        </v-card-title>
        <v-card-text>
            <div>
              <p class="adr">{{hotel.address}}</p>
            </div>
          </v-card-text>
        <v-card-actions>
          <v-btn flat color="white" :to="`/hotels/${hotel.id}`">Book Now</v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</v-container>
      </div>
    </div>
  </div>
</template>

<style lang="stylus">
a
  text-decoration none
.progress-linear__background
  width unset !important
.htl-name
  font-size: 1.2em
  font-weight: 300
.adr
  font-size: 1em
  // @media screen and (max-width: 1264px)
  //    font-size: 0.7em

  font-weight: 100
  color: #fff

</style>

<script>
  export default {
    name: 'Hotels',
    props: ['loggedIn', 'user'],
    data() {
      return {
        loading: true,
        error: {
          is: false,
          whattosay: ''
        },
        hotels: []
      }
    },
    methods: {
      getHotels() {
        this.$http.get(`//${this.api}/hotels`).then(data => {
          this.loading = false
          if(typeof data !== 'undefined' && typeof data.data !== 'undefined' && data.data.hotels !== null && typeof data.data.hotels.length !== 'undefined') {
            this.hotels = data.data.hotels
          } else {
            this.error.is = true
            this.error.whattosay = 'Cannot Fetch Hotels right now, Please Try again later? Please?!'
          }
        }).catch(e => {
          this.loading = false
          this.error.is = true
          this.error.whattosay = 'Cannot Fetch Hotels right now, Please Try again later? Please?!'
          console.error(e)
        })
      }
    },
    beforeMount() {
      setTimeout(() => {
        this.getHotels()
      }, 1000)
    }
  }
</script>
