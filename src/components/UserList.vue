<script setup lang="ts">
import type { User } from '@/types/users';

const props = defineProps<{
  filterUser: User[];
}>();

const emit = defineEmits(['uploadImage']);

function uploadImage(event: Event, index: number) {
  const target = event.target as HTMLInputElement;
  if (target && target.files && target.files.length > 0) {
    emit('uploadImage', target.files[0], index);
  }
}

function onDrop(event: DragEvent, index: number) {
  event.preventDefault();
  if (event.dataTransfer && event.dataTransfer.files.length > 0) {
    emit('uploadImage', Array.from(event.dataTransfer.files)[0], index);
  }
}
</script>

<template>
	<ul class="users">
		<TransitionGroup name="list">
			<li
				v-for="(user, index) in filterUser"
				:key="user.id" class="users__item"
				@dragover.prevent
				@dragenter.prevent
				@drop.prevent="onDrop($event, index)"
			>
				<img :src="user.image" alt="" style="width: 40px; height: 40px;" class="users__item-image">
				<div class="users__item-info">
					<p>Имя - {{ user.name }}</p>
					<p>Возраст - {{ user.age }}</p>
					<p>Email - {{ user.email }}</p>
					<label class="file">
						Нажмите или перетащите файл сюда
						<input
							id="user_avatar"
							style="display: none"
							type="file"
							accept="image/png"
							@change="uploadImage($event, index)"
						>
					</label>
				</div>
			</li>
		</TransitionGroup>
	</ul>
</template>

<style scoped>
.users{
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap:20px
}

.users__item{
  display: flex;
  gap: 20px;
}

.users__item-info{
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.users__item-image{
  width: 40px;
  height: 40px;
  object-fit: cover;
}

.file{
  border:1px solid black;
  padding: 5px;
  cursor: pointer;
  color:white;
  background-color: rgb(95, 94, 93);
}

.file:hover{
  background-color: rgba(95, 94, 93,0.8);
}
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

@media (max-width: 1024px) {
  .users{
    grid-template-columns: 1fr 1fr ;
  }
}
@media (max-width: 768px) {
  .users{
    grid-template-columns: 1fr;
  }
}
</style>
