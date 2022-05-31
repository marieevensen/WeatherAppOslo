<template>
	<main class="weather" v-if="!error">
		<div class="weather__today">
			<h1 class="today__title">OSLO {{ temp }}</h1>

			<p>Description: {{ text }}</p>

			<p>Wind: {{ breeze }}</p>
		</div>

		<div class="weather__days">
			<article class="days__box" v-for="day in days">
				<h3 class="box__text">Day {{ day.day }}</h3>

				<p class="box__text--temperature">{{ day.temperature }}</p>
				
				<p class="box__text">{{ day.wind }}</p>
			</article>
		</div>
	</main>

	<div v-if="error">
		{{ error }}
	</div>
</template>

<script>
	export default {
		data () {
	 		return {
				temp: '',
				breeze: '',
				text: '',
				days: [],
				error: ''
	 		}
	 	},

		created() {
			this.fetchValue();
		},

	 	methods: {
	 		async fetchValue() {
	 			const url = `https://goweather.herokuapp.com/weather/oslo`;
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
					throw new Error('Noe gikk galt!');
				}
			}
		}
	}
</script>

<!-- Kommenterer script
	1 Henter ut info fra api
	2 Sjekker om status er større eller lik 200 OG mindre enn 300
	2.1 Hvis status er det så hentes alle resultatene jeg trenger fra api
	2.2 Gir resultatene nye navn, som this.temp = result.temperature
	3 Hvis ikke 2 er true, så sjekkes det hvilken feilmelding som gjelder og skriver ut en errormelding
-->

<style>
	.weather {
		display: flex;
		flex-flow: column;
		align-items: center;
		padding-top: 100px;
	}

	@media screen and (max-width: 650px) {
		.weather {
			padding-top: 40px;
		}
	}

	.weather__today {
		padding-bottom: 30px;
	}

	.today__title {
		font-size: 60px;
	}

	@media screen and (max-width: 650px) {
		.today__title {
			font-size: 30px;
		}
	}

	.weather__days {
		display: flex
	}

	@media screen and (max-width: 650px) {
		.weather__days {
			display: inline;
		}
	}

	.days__box {
		width: 200px;
		height: 250px;
		margin: 5px;
		padding-top: 10px;
		text-align: center;
		background-color: var(--details);
	}

	@media screen and (max-width: 650px) {
		.days__box {
			width: 250px;
		}
	}

	.box__text {
		padding-top: 20px;
	}

	.box__text--temperature {
		padding-top: 20px;
		font-size: 50px;
		font-weight: bold;
	}
</style>