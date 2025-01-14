<template>
  <div class="damage-records">
    <img :src="book?.imgThumbnail" alt="Damaged Book" />
  </div>
</template>

<script setup lang="ts">
import { defineProps } from 'vue'

interface Book {
  imgThumbnail?: string // imgThumbnail 속성 추가
}

const props = defineProps<{
  book: Book | null // book이 null일 수 있도록 수정
}>()
</script>

<style scoped>
.damage-records {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  padding: 16px;
  height: 500px;
  box-sizing: border-box;

  display: flex;
  justify-content: center;
  align-items: center;
}

.damage-records img {
  width: 250px;
  border-radius: 8px;
}
</style>
