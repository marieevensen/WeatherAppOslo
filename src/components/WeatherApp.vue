<template>
	<main class="weather">
		<div class="weather__today">
			<div class="today__header">
				<h1 class="header__city">OSLO</h1>

				<h1>{{ temp }}</h1>
			</div>

			<div class="today__description">
				<h4 class="description__text">Description: {{ text }}</h4>

				<h4>Wind: {{ breeze }}</h4>
			</div>
		</div>

		<div class="weather__days">
			<div class="days__box" v-for="day in days">
				<h5 class="box__text">Day {{ day.day }}</h5>

				<h5 class="box__text box__text--degree">{{ day.temperature }}</h5>
				
				<h5 class="box__text">{{ day.wind }}</h5>
			</div>
		</div>
	</main>
</template>

<script>
	export default {
		data () {
	 		return {
				temp: '',
				breeze: '',
				text: '',
				days: []
	 		}
	 	},

		created() {
			this.fetchValue();
		},

	 	methods: {
	 		async fetchValue() {
	 			const url = `https://goweather.herokuapp.com/weather/osl`;
	 			const respond = await fetch(url);
				try {
					await this.handleRespond(respond);
				} catch (error) {
					this.error = error.message;
				}
			},

			async handleRespond(respond) {
				if(respond.status >= 200 && respond.status < 300) {
					const result  = await respond.json();
					this.temp = result.temperature
					this.breeze = result.wind
					this.text = result.description
					this.days = result.forecast
				
					return true;
					
				} else {
					if(respond.status === 404) {
						throw new Error('Url ikke funnet!');
					}
					if(respond.status === 401) {
						throw new Error('Ikke authorisert!');
					}
					if(respond.status > 500) {
						throw new Error('Server error!');
					}
					throw new Error('noe gikk galt!');
				}
			}
		}
	}
</script>

<style>
	.weather {
		display: flex;
		flex-flow: column;
		align-items: center;
	}

	.today__header {
		padding-top: 30px;
		font-size: 20px;
		display: flex;
		text-align: center;
	}

	@media screen and (min-width: 600px) {
		.today__header {
			display: flex;
			padding-top: 100px;
			font-size: 40px;
		}
	}

	.header__city {
		padding-right: 100px;
	}

	.today__description {
		margin-bottom: 30px;
	}

	@media screen and (min-width: 600px) {
		.today__description {
			display: flex;
			margin-bottom: 50px;
		}
	}

	.description__text {
		padding-right: 130px;
	}

	@media screen and (max-width: 600px) {
		.description__text {
			padding-right: 10px;
		}
	}

	@media screen and (min-width: 650px) {
		.weather__days {
			display: flex;
		}
	}

	.days__box {
		width: 200px;
		height: 250px;
		margin: 5px;
		padding-top: 10px;
		text-align: center;
		background-color: lightblue;
		font-size: 30px;
	}

	.box__text {
		padding-top: 20px;
	}

	.box__text--degree {
		font-size: 60px;
	}
</style>