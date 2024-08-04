<script setup lang="ts">
import playList from '../../public/music/playList';
import { onMounted, ref } from 'vue';

const audio = ref<HTMLAudioElement | null>(null);

const isPlaying = ref(false);

const currentTrackIndex = ref(0);

onMounted(() => {
	if (audio.value) {
		audio.value.src = playList[currentTrackIndex.value].src;
		audio.value.volume = 0.1;
	}
});

const togglePlay = () => {
	if (audio.value) {
		if (isPlaying.value) {
			audio.value.pause();
		} else {
			audio.value.play();
		}
		isPlaying.value = !isPlaying.value;
	}
};

const playTrack = (index: number) => {
	currentTrackIndex.value = index;
	if (audio.value) {
		audio.value.src = playList[index].src;
		audio.value.play();
	}
};

const handleClick = (index: number) => {
	// if (selectedIndex.value === index) {
	// 	audio.value?.pause();
	// } else {
	// 	audio.value?.play();
	// }
	playTrack(index);
	togglePlay();
};
</script>

<template>
	<div class="absolute top-4 left-4 flex flex-col gap-6">
		<audio ref="audio"></audio>
		<div class="flex gap-3">
			<button
				@click="
					playTrack((currentTrackIndex - 1 + playList.length) % playList.length)
				"
				class="w-10"
			>
				<img src="/play-prev.svg" alt="prv" />
			</button>
			<button @click="togglePlay()" class="w-12">
				<img :src="isPlaying ? '/pause.svg' : '/play.svg'" alt="play" />
			</button>
			<button
				@click="playTrack((currentTrackIndex + 1) % playList.length)"
				class="w-10"
			>
				<img src="/play-next.svg" alt="" />
			</button>
		</div>

		<div class="">
			<ul class="flex flex-col items-start gap-2">
				<li
					v-for="(item, index) in playList"
					:key="index"
					@click="handleClick(index)"
					:class="[
						'flex flex-row-reverse gap-3 items-center text-xl  cursor-pointer',
						currentTrackIndex === index && isPlaying
							? 'text-purple-500'
							: 'text-white',
					]"
				>
					{{ item.title }}
				</li>
			</ul>
		</div>
	</div>
</template>
