<script setup lang="ts">
import axios from 'axios';
import { ref, onMounted } from 'vue';

const city = ref('');

const weatherData = ref<any>(null);

const getWeather = async () => {
	try {
		const response = await axios.get(
			`https://api.openweathermap.org/data/2.5/weather?q=${city.value}&lang=en&appid=08f2a575dda978b9c539199e54df03b0&units=metric`
		);
		weatherData.value = response.data;
		localStorage.setItem('lastCity', city.value);
		localStorage.setItem('lastWeatherData', JSON.stringify(weatherData.value));
	} catch (err) {
		console.log(err);
		weatherData.value = null;
	}
};

const loadSavedData = () => {
	const savedCity = localStorage.getItem('lastCity');
	const savedWeaterData = localStorage.getItem('lastWeatherData');

	if (savedCity) {
		city.value = savedCity;
	}

	if (savedWeaterData) {
		try {
			weatherData.value = JSON.parse(savedWeaterData);
		} catch (err) {
			console.log(err);
			weatherData.value = null;
		}
	}
};

onMounted(() => {
	loadSavedData();
	getWeather();
});
</script>

<template>
	<div class="absolute flex flex-col gap-4 right-4 top-4">
		<input
			v-model="city"
			@keyup.enter="getWeather"
			placeholder="Write City"
			type="text"
			class="bg-transparent border-b-2 border-white text-2xl placeholder-white/50 focus:outline-none text-white focus:border-sky-500 transition-colors w-full"
		/>

		<div v-if="weatherData" class="text-xl text-white">
			<img
				:src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
				:alt="weatherData.weather[0].description"
				class="w-16 h-16"
			/>
			<p>
				{{ Math.round(weatherData.main.temp) }}°C
				{{ weatherData.weather[0].description }}
			</p>
			<p>Wind speed: {{ weatherData.wind.speed }} m/s</p>
			<p>Humidity: {{ weatherData.main.humidity }}%</p>
		</div>
	</div>
</template>
