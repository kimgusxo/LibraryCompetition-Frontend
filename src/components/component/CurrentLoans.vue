<template>
  <div class="current-loans">
    <h3>현재 대출 도서 목록</h3>
    <div class="loaned-books-wrapper">
      <div
        class="loaned-books"
        :style="{ transform: `translateX(-${currentIndex * bookWidth}px)` }"
      >
        <img v-for="(img, index) in loanedBookImages" :key="index" :src="img" alt="Book Cover" />
      </div>
    </div>
    <div class="controls">
      <button @click="prev" :disabled="currentIndex === 0" class="nav-button">◀</button>
      <button @click="next" :disabled="currentIndex === maxIndex" class="nav-button">▶</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useStore } from 'vuex'

const store = useStore()
const memberId = '1' // 예시 멤버 ID
const bookWidth = 110 // 책 이미지의 너비 + 마진
const currentIndex = ref(0)
const booksPerPage = 2

// Vuex에서 대출 기록과 책 리스트 가져오기
const loanRecords = computed(() => store.getters.getLoanRecords)
const bookList = computed(() => store.getters.getBookList)

// 현재 대출 중인 책들의 이미지를 가져오기
const loanedBookImages = computed(() => {
  const filteredLoans = loanRecords.value.filter((loan) => !loan.returnTime)
  return filteredLoans
    .map((loan) => {
      const book = bookList.value.find((book) => book.bookId === loan.bookId)
      return book?.imgThumbnail || '' // imgThumbnail 반환, 없으면 빈 문자열
    })
    .filter(Boolean) // 유효한 값만 반환
})

const maxIndex = computed(() => {
  const totalWidth = loanedBookImages.value.length * bookWidth
  const visibleWidth = booksPerPage * bookWidth
  return Math.ceil((totalWidth - visibleWidth) / bookWidth)
})

const prev = () => {
  if (currentIndex.value > 0) currentIndex.value--
}

const next = () => {
  if (currentIndex.value < maxIndex.value) currentIndex.value++
}

// Vuex 액션으로 데이터 로드
onMounted(() => {
  store.dispatch('fetchLoanRecordsByMemberId', memberId)
  store.dispatch('fetchBooks')
})
</script>

<style scoped>
.current-loans {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: #fff;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  padding: 10px;
}

.loaned-books-wrapper {
  align-items: center;
  overflow: hidden;
  width: 300px;
  height: 150px;
}

.loaned-books {
  display: flex;
  transition: transform 0.3s ease-in-out;
}

.loaned-books img {
  width: 100px;
  height: auto;
  margin-right: 10px;
}

.controls {
  display: flex;
  gap: 10px;
  margin-top: 10px;
}

.nav-button {
  background: transparent;
  border: none;
  padding: 10px;
  font-size: 1.5rem;
  color: #333;
  cursor: pointer;
  transition:
    background 0.3s ease,
    color 0.3s ease;
}

.nav-button:disabled {
  color: #ccc;
  cursor: not-allowed;
}

.nav-button:not(:disabled):hover {
  background: #f0f0f0;
  border-radius: 50%;
}
</style>
