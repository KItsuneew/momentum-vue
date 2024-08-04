<script setup lang="ts">
import { onMounted, ref } from 'vue';

const isVisibilityMenu = ref(false);

const isVisibilityInput = ref(false);

const isVisibilityModal = ref(false);

const todoInput = ref<HTMLInputElement | null>(null);

const itemsTodo = ref<string[]>([]);

const completeTodo = ref<string[]>([]);

const currentItem = ref('');

const onClickTodoMenu = () => {
	isVisibilityMenu.value = !isVisibilityMenu.value;
};

const onClickToModal = () => {
	isVisibilityModal.value = !isVisibilityModal.value;
};

const onClickMenu = () => {
	isVisibilityInput.value = !isVisibilityInput.value;
	setTimeout(() => {
		todoInput.value?.focus();
	}, 0);
};

const addItemsTodo = () => {
	itemsTodo.value.push(currentItem.value);
	currentItem.value = '';
	localStorage.setItem('itemsArrayTodo', JSON.stringify(itemsTodo.value));
};

const loadItemsTodo = () => {
	const currentItems = localStorage.getItem('itemsArrayTodo');
	if (currentItems) {
		itemsTodo.value = JSON.parse(currentItems);
	}
};

const saveItemsTodo = () => {
	localStorage.setItem('itemsArrayTodo', JSON.stringify(itemsTodo.value));
};

const clickDeleteTodo = (index: number) => {
	itemsTodo.value.splice(index, 1);
	saveItemsTodo();
};

const clickCompleteTodo = (index: number) => {
	const complete = itemsTodo.value.splice(index, 1)[0];
	completeTodo.value.push(complete);
	saveItemsTodo();
};

onMounted(() => {
	loadItemsTodo();
});
</script>

<template>
	<div
		v-if="isVisibilityModal"
		:class="[
			'bg-[#0f0f0fec] rounded-lg w-80 h-auto text-white absolute transition-transform bottom-14 right-3',
			isVisibilityModal ? '-translate-x-80' : 'translate-x-0',
		]"
	>
		<h2 class="text-center opacity-70">Complete your task</h2>
		<ul class="flex flex-col gap-2 px-3 py-4">
			<li v-for="item in completeTodo">{{ item }}</li>
		</ul>
	</div>
	<div class="absolute bottom-4 right-3 flex flex-col items-end gap-3">
		<div v-if="isVisibilityMenu" class="bg-[#0f0f0fec] rounded-lg w-80 h-auto">
			<div class="text-white flex flex-col items-center gap-3">
				<div
					v-if="!itemsTodo.length"
					class="flex flex-col items-center gap-3 p-8"
				>
					<h2 class="opacity-70">Add to todo get started</h2>
					<button
						:disabled="isVisibilityInput"
						@click="onClickMenu"
						:class="[
							'bg-[#3a4e6ef2] px-4 py-2 rounded-lg transition-opacity duration-300',
							!isVisibilityInput ? 'opacity-100' : 'opacity-0',
						]"
					>
						New Todo
					</button>
				</div>
			</div>
			<div v-if="itemsTodo.length" class="px-4 py-3">
				<ol class="flex flex-col items-start text-white gap-2">
					<li class="w-full" v-for="(item, index) in itemsTodo">
						<span :key="index" class="flex items-center gap-1">
							<span>{{ item }}</span>
							<div class="flex items-center gap-3 ml-auto">
								<button @click="clickDeleteTodo(index)" class="">
									<img class="w-4 h-4" src="/trash.svg" alt="trash" />
								</button>
								<button @click="clickCompleteTodo(index)" class="">
									<img class="w-4 h-4" src="/complete.svg" alt="complete" />
								</button>
							</div>
						</span>
					</li>
				</ol>
			</div>
			<div
				:class="[
					!itemsTodo.length && !isVisibilityInput ? 'opacity-0' : 'opacity-100',
					'transition-opacity duration-300',
					'p-3',
				]"
			>
				<input
					v-model="currentItem"
					@keyup.enter="addItemsTodo"
					ref="todoInput"
					class="w-full text-white bg-transparent outline-none"
					type="text"
					placeholder="New Todo"
				/>
				<button @click="onClickToModal">
					<img class="w-4 h-4" src="/arrow_double.svg" alt="" />
				</button>
			</div>
		</div>

		<button @click="onClickTodoMenu" class="text-xl text-white">Todo</button>
	</div>
</template>
