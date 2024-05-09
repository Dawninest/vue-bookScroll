<template>
  <div class="container">
    <div class="title">
      BookScroll Demo
    </div>
    <div class="context">
      <div class="tipBox">
        <div class="tip">示例比例为 {{bookPageW}}*{{bookPageH}} (1:2) 标准移动端比例</div>
        <button class="workButton" @click="doScroll">翻页</button>
      </div>
      <div
          class="showBox"
          :style="{
            width: `${bookPageW}px`,
            height: `${bookPageH}px`,
          }"
      >
        <BookScroll
            ref="bookScrollRef"
            :book-width="bookPageW"
            :book-height="bookPageH"
            :page-length="bookPageArr.length"
        >
          <template #topPage>
            <div
                class="showPageBox"
                :style="{
                  width: `${bookPageW}px`,
                  height: `${bookPageH}px`,
                }"
            >
              <img v-if="topPage" :src="topPage.img"/>
            </div>
          </template>
          <template #bottomPage>
            <div
                class="showPageBox"
                :style="{
                  width: `${bookPageW}px`,
                  height: `${bookPageH}px`
                }"
            >
              <img v-if="bottomPage" :src="bottomPage.img"/>
            </div>
          </template>
        </BookScroll>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import BookScroll from "./bookScroll.vue";
import { ref, onMounted } from "vue";

const bookScrollRef = ref();

const bookPageW = 375;
const bookPageH = 750;

const bookPageArr = [
  { img: "/src/assets/img/page1.jpg", page: 1 },
  { img: "/src/assets/img/page2.jpg", page: 2 },
  { img: "/src/assets/img/page3.jpg", page: 3 },
  { img: "/src/assets/img/page4.jpg", page: 4 },
  { img: "/src/assets/img/page5.jpg", page: 5 },
];

const pageIndex = ref(0);
const topPage = ref<any>();
const bottomPage = ref<any>();

onMounted(() => {
  resetBook();
})


const resetBook = () => {
  if (pageIndex.value === bookPageArr.length - 1) {
    // 当前是最后一个
    topPage.value = bookPageArr[pageIndex.value];
    bottomPage.value = bookPageArr[0];
    pageIndex.value = -1;
  } else {
    topPage.value = bookPageArr[pageIndex.value];
    bottomPage.value = bookPageArr[pageIndex.value + 1];
  }
}

const doScroll = () => {
  bookScrollRef.value?.scroll(() => {
    pageIndex.value += 1;
    console.log("@@@nextPage:", pageIndex.value);
    resetBook();
  });
}
</script>

<style scoped lang="scss">
.container {
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  background: #F6F7F9;
  .title {
    font-size: 30px;
    color: #3B71E8;
    padding: 20px;
  }
  .context {
    display: flex;
    flex-direction: row;
    padding: 20px;
    flex: 1;
    .tipBox {
      margin-right: 20px;
      .workButton {
        height: 36px;
        padding: 6px 12px;
        border: 1px solid black;
        background-color: white;
        border-radius: 6px;
        font-size: 20px;
        color: black;
      }
    }
    
    .showBox {
      border: 1px solid #E6E6E6;
      .showPageBox {
        > img {
          width: 100%;
          height: 100%;
          object-fit: contain;
        }
      }
    }
  }
}
</style>
