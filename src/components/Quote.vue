<script setup lang="ts">
import axios from 'axios';
import { ref, onMounted } from 'vue';

const name = ref('');
const quote = ref('');
const isRotated = ref(false);

const fetchQuote = async () => {
	try {
		const response = await axios.get('/quotes/quotes.json');
		const quotes = response.data;
		const randomIndex = Math.floor(Math.random() * quotes.length);
		quote.value = quotes[randomIndex].text;
		name.value = quotes[randomIndex].author;
		isRotated.value = !isRotated.value;
	} catch (error) {
		console.error('Error fetching quote:', error);
	}
};

onMounted(() => {
	fetchQuote();
});
</script>

<template>
	<div
		class="text-white text-xl justify-center flex flex-col gap-3 items-center w-full absolute bottom-10"
		v-if="quote"
	>
		<button
			@click="fetchQuote"
			:class="[
				'transition-transform duration-500',
				{ 'rotate-180': isRotated },
			]"
		>
			<img src="/reload.svg" alt="" />
		</button>
		<p class="w-1/2 text-center">"{{ quote }}"</p>
		<p class="italic">{{ name }}</p>
	</div>
</template>
