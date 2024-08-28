<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import UserList from './components/UserList.vue';

import type { User } from './types/users';
import type { UsersFetch } from './types/usersFetch';

const users = ref<User[]>();
const nameUser = ref('');
const ageUser = ref(1);
const isSortName = ref(false);
const isSortAge = ref(false);
onMounted(async () => {
  const resp = await fetch('https://jsonplaceholder.typicode.com/users');
  const data = await resp.json();
  users.value = data.map((user: UsersFetch) => {
      return {
        id: user.id,
        name: user.name,
        email: user.email,
        age: getRandomArbitrary(16, 20),
        image: new URL(`./assets/person.png`, import.meta.url).href,
      };
  });
});

function getRandomArbitrary(min: number, max: number) {
  return Math.round(Math.random() * (max - min) + min);
}

const filterUser = computed(() => {
  if (users.value) {
    return users.value.filter((user) => {
      const name = user.name.includes(nameUser.value);
      const age = user.age >= ageUser.value;
      return name && age;
    });
  }
  return [];
});

function uploadImage(file: File, index: number) {
  const reader = new FileReader();
  reader.onload = (e: ProgressEvent<FileReader>) => {
    const result = e.target?.result;
    if (result && users.value) {
      if (typeof result === 'string') {
        users.value[index].image = result;
      }
    }
  };
  reader.readAsDataURL(file);
}

function sortByName() {
  isSortName.value = !isSortName.value;
  if (users.value) {
    users.value.sort((a, b) => {
      if (a.name.toLowerCase() < b.name.toLowerCase()) {
        return isSortName.value ? -1 : 1;
      }
      if (a.name.toLowerCase() > b.name.toLowerCase()) {
        return isSortName.value ? 1 : -1;
      }
      return 0;
    });
  }
}

function sortByAge() {
  isSortAge.value = !isSortAge.value;
  if (users.value) {
    users.value.sort((a, b) => {
      if (a.age < b.age) {
        return isSortAge.value ? -1 : 1;
      }
      if (a.age > b.age) {
        return isSortAge.value ? 1 : -1;
      }
      return 0;
    });
  }
}
</script>

<template>
	<main v-if="users" class="main">
		<div class="filters">
			<input v-model="nameUser" type="text" placeholder="Фильтрация по имени" class="filters__name">
			<label for="">
				<input v-model="ageUser" type="checkbox" true-value="18" false-value="1">18+
			</label>
		</div>
		<div class="sorted">
			<p>Сортировать:</p>
			<button class="sorted__button" @click="sortByName">
				По имени
			</button>
			<button class="sorted__button" @click="sortByAge">
				По возрасу
			</button>
		</div>
		<UserList :filter-user="filterUser" @upload-image="uploadImage" />
	</main>
	<p v-else>
		Loading...
	</p>
</template>

<style scoped>
.main{
  border:1px solid black;
  border-radius: 20px;
  box-shadow: 10px 10px 5px 0px rgba(0,0,0,0.75);
  display:flex;
  flex-direction: column;
  justify-items: center;
  align-items: center;
  gap:20px;
  padding: 10px;
}

.filters , .sorted{
  display: flex;
  align-items: center;
  gap: 20px;
}

.filters__name{
  padding: 5px;
  border-radius: 10px;
  outline: none;
  border:2px solid rgb(86, 85, 85)
}

.filters__name:focus{
  border:2px solid blue
}

.sorted__button{
  border:1px solid black;
  padding: 5px;
  cursor: pointer;
  color:white;
  background-color: rgb(95, 94, 93);
}

.sorted__button:hover{
  background-color: rgba(95, 94, 93,0.8);
}
</style>
