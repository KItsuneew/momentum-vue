<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue';
let timer: number;

const body = document.querySelector('body');
const currentName = ref('');
const currentTime = ref('');
const currentDate = ref('');
const greeting = ref('');
const backgroundImage = ref('');

const currentIdImage = ref(Math.floor(Math.random() * (20 - 1) + 1));

const updateTime = () => {
	const now = new Date();
	currentTime.value = now.toLocaleTimeString('en-US', { hour12: false });
	currentDate.value = now.toLocaleDateString('en-US', {
		weekday: 'long',
		month: 'long',
		day: 'numeric',
	});

	const hour = now.getHours();
	if (hour < 12) greeting.value = 'Morning';
	else if (hour < 18) greeting.value = 'Afternoon';
	else greeting.value = 'Evening';
	console.log(hour);
};

const updateBackgroundImage = () => {
	backgroundImage.value = `url(/images/${greeting.value}/${currentIdImage.value}.jpg)`;
	if (body) {
		body.style.backgroundImage = backgroundImage.value;
	}
};

const savedName = () => {
	localStorage.setItem('currentName', currentName.value);
};

const reloadName = () => {
	const name = localStorage.getItem('currentName');
	currentName.value = name ?? '';
};

const prevSlider = () => {
	if (currentIdImage.value == 1) {
		currentIdImage.value = 20;
	} else {
		currentIdImage.value--;
	}
	console.log(currentIdImage.value);
};

const nextSlider = () => {
	if (currentIdImage.value == 20) {
		currentIdImage.value = 1;
	} else {
		currentIdImage.value++;
	}
	console.log(currentIdImage.value);
};

onMounted(() => {
	updateTime();
	reloadName();
	updateBackgroundImage();
	reloadName();
	timer = setInterval(updateTime, 1000);
});

onUnmounted(() => {
	clearInterval(timer);
});

watch(currentIdImage, updateBackgroundImage);
</script>

<template>
	<div class="flex justify-between items-center h-screen px-3">
		<button @click="prevSlider()" class="w-8 h-8">
			<img src="/slider-prev.svg" alt="slider" />
		</button>
		<button @click="nextSlider()" class="w-8 h-8">
			<img src="/slider-next.svg" alt="slider" />
		</button>
	</div>
	<div
		class="flex flex-col items-center justify-center absolute m-auto gap-5 text-white top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2"
	>
		<p class="text-8xl tracking-widest">{{ currentTime }}</p>
		<p class="text-2xl">{{ currentDate }}</p>

		<div class="flex gap-4 items-center">
			<p class="text-4xl">Good {{ greeting }},</p>

			<input
				v-model="currentName"
				@input="savedName"
				class="text-white outline-none text-4xl placeholder-gray-400 bg-transparent max-w-56 text-left"
				placeholder="[Enter name]"
				type="text"
			/>
		</div>
	</div>
</template>
