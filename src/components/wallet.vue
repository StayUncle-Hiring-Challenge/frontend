<template>
	<div>
    <v-snackbar
      :timeout="150000"
      bottom
      multi-line
      v-model="snackbar"
    >
      {{ text }}
      <v-btn flat color="pink" @click.native="snackbar = false">Close</v-btn>
    </v-snackbar>
		<div v-show="error.is || !loggedIn" class="main">
			<h2>{{error.text}}</h2>
		</div>
		<v-container grid-list-md text-xs-center v-show="loggedIn && !error.is">
			<h2>Welcome to Your Wallet</h2>
			<v-layout row wrap>
				<v-flex xs12>
					<v-card dark color="light-green darken 3">
						<v-card-text class="px-0" style="font-size: 1em;">
							Wallet Operations and Status
						</v-card-text>
					</v-card>
				</v-flex>
				<v-flex xs6>
					<v-card dark color="red">
						<v-card-text class="px-0">
							Details
						</v-card-text>
					</v-card>
					<div style="padding-top: 1%;">
						<h3 style="font-size: 30px;">Name: {{user.name}}</h3>
						<h3 style="font-size: 30px;">Balance: ₹{{balance}}</h3>
					</div>
				</v-flex>
				<v-flex xs6>
					<v-card dark color="blue">
						<v-card-text class="px-0">
							Operations
						</v-card-text>
					</v-card>
					<div style="padding-top: 1%;">
						<v-text-field
						name="amt"
						label="Amount In Rupees"
						type="number"
						min=0
						max=10000
						v-model="rech"
						@keyup.enter="recharge"
						></v-text-field>
						<v-btn color="blue" dark @click="recharge">Recharge</v-btn>
					</div>
				</v-flex>
				<v-flex xs12>
					<v-card dark color="dark">
						<v-card-text class="px-0">
							Bookings
						</v-card-text>
					</v-card>
        </v-toolbar>
				<v-list>
					<v-list-tile avatar v-for="(booking, i) in bookings" v-bind:key="i" @click="">
						<v-list-tile-action>
							<v-icon color="pink">business</v-icon>
						</v-list-tile-action>
						<v-list-tile-content>
							<v-list-tile-title>Hotel: {{booking.hotel.name}}, Charges: {{booking.hotel.dailyRate}}, Tx: {{booking.id}}</v-list-tile-title>
						</v-list-tile-content>
						<v-list-tile-avatar>
							<img v-bind:src="booking.hotel.image"/>
						</v-list-tile-avatar>
					</v-list-tile>
				</v-list>
				</v-flex>
			</v-layout>
		</v-container>
	</div>
</template>

<script>
export default {
  props: ['loggedIn', 'user'],
  data() {
	  return {
		  error: {
			  is: false,
			  text: 'Please Login to Access the Wallet.'
		  },
		  balance: 0,
		  rech: 0,
		  snackbar: false,
		  text: '',
		  bookings: []
	  }
  },
  methods: {
	  getWallet() {
		  this.$socket.emit('walletBalance')
		  this.$socket.emit('getBookings')
	  },
	  recharge() {
		  this.$socket.emit('rechargewallet', this.rech)
	  }
  },
  mounted() {
	  this.getWallet()
  },
  socket: {
	  events: {
		  returnWalletBalance(amt) {
			  this.balance = amt
		  },
		  returnRechargeWallet(value) {
			  this.balance = value
			  this.rech = 0
			  this.text = 'Wallet has been recharged'
			  this.snackbar = true
		  },
		  returnBookings(items) {
			  this.bookings = items.reverse()
		  }
	  }
  }
}
</script>


<style lang="stylus" scoped>
h2
	font-size 4em
</style>