<template>
  <div
      class="boxContainer"
      :style="{
        height: `${props.bookHeight}px`,
        width: `${props.bookWidth}px`,
      }"
  >
    <div
        class="bookScrollBox"
        :style="{
          height: `${props.bookHeight}px`,
          width: `${props.bookWidth * 2}px`,
          left: `${-props.bookWidth}px`,
        }"
    >
      <div
          class="bottomPage"
          :style="{
            height: `${props.bookHeight}px`,
            width: `${props.bookWidth}px`,
            left: `${props.bookWidth}px`,
            transformOrigin: `${props.bookWidth}px ${
              props.bookHeight + props.bookWidth
            }px`,
          }"
      >
        <slot name="bottomPage"/>
      </div>
      <div
          :class="{ topPage: true, topPageScroll: bookScroll, noAnimate: hiddenAnimate }"
          :style="{
            height: `${props.bookHeight}px`,
            width: `${props.bookWidth}px`,
            left: `${props.bookWidth}px`,
          }"
      >
        <slot name="topPage"/>
      </div>
      <div
          :class="{ divC: true, scrollC: bookScroll,noAnimate: hiddenAnimate }"
          :style="{
            height: `${props.bookHeight}px`,
            width: `${props.bookWidth}px`,
            transformOrigin: `${props.bookWidth}px ${
              props.bookHeight + props.bookWidth
            }px`,
          }"
      >
        <div class="divCI">
          <div
              :class="{ divL: true, scrollL: bookScroll,noAnimate: hiddenAnimate }"
              :style="{
                height: `${(props.bookHeight + props.bookWidth) * 1.2}px`,
                width: `${props.bookWidth}px`,
                left: `${props.bookWidth}px`,
                top: `${-(props.bookHeight + props.bookWidth) * 0.2}px`,
              }"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import {ref, defineExpose, withDefaults, defineProps} from "vue";

const props = withDefaults(
    defineProps<{
      bookWidth: number;
      bookHeight: number;
      duration: number; // 1s 600ms
    }>(),
    {
      bookWidth: 100,
      bookHeight: 100,
      duration: 600,
    }
);

const bookScroll = ref(false);
const hiddenAnimate = ref(false)
const inScrolling = ref(false);

const onScroll = (callBack?: () => void) => {
  if (inScrolling.value || bookScroll.value) {
    return;
  }
  inScrolling.value = true;
  setTimeout(() => {
    bookScroll.value = !bookScroll.value;
  }, 0)
  setTimeout(() => {
    hiddenAnimate.value = true;
    inScrolling.value = false;
    onReset(callBack);
  }, props.duration)
};

const onReset = (callBack?: () => void) => {
  inScrolling.value = false;
  bookScroll.value = false;
  callBack && callBack();
  setTimeout(() => {
    hiddenAnimate.value = false;
  }, 50)
}

// -0.41(w+h)
const polygonNumOne = -0.4 * (props.bookHeight + props.bookWidth) + "px";
const polygonNumTwo = (props.bookHeight + props.bookWidth) + "px";
const animationDuring = props.duration + "ms";

defineExpose({
  name: "bookScroll",
  scroll: onScroll,
  reset: onReset
});
</script>

<style lang="scss" scoped>
.boxContainer {
  overflow: hidden;
}

.bookScrollBox {
  position: relative;
  
  .bottomPage {
    position: absolute;
    //background: black;
  }
  
  // 0.41 即 根号二减一
  // (0 -0.41(w+h), 0 w+h, w+h 0)
  // (0 -0.41(w+h), 0 w+h, 0 -0.41(w+h))
  
  .topPage {
    position: absolute;
    //background: red;
    clip-path: polygon(0 v-bind(polygonNumOne), 0 v-bind(polygonNumTwo), v-bind(polygonNumTwo) 0);
  }
  
  .topPageScroll {
    animation: morph v-bind(animationDuring) linear 0s;
    clip-path: polygon(0 v-bind(polygonNumOne), 0 v-bind(polygonNumTwo), 0 v-bind(polygonNumOne));
  }
  
  @keyframes morph {
    0% {
      clip-path: polygon(0 v-bind(polygonNumOne), 0 v-bind(polygonNumTwo), v-bind(polygonNumTwo) 0);
    }
    100% {
      clip-path: polygon(0 v-bind(polygonNumOne), 0 v-bind(polygonNumTwo), 0 v-bind(polygonNumOne));
    }
  }
  
  .divC {
    position: absolute;
    transform: rotateZ(90deg);
    transition: all v-bind(animationDuring) linear 0s;
    display: flex;
    //border: 1px solid gray;
  }
  
  .scrollC {
    transform: rotateZ(0deg);
  }
  
  .divCI {
    flex: 1;
    position: relative;
    background: transparent;
    overflow: hidden;
  }
  
  
  .divL {
    border: 1px solid white;
    position: absolute;
    transform-origin: bottom left;
    transform: rotateZ(-45deg) rotateY(180deg);
    transition: all v-bind(animationDuring) linear 0s;
    background: #F4F5F7;
  }
  
  .scrollL {
    transform: rotateZ(0) rotateY(180deg);
  }
  
  .noAnimate {
    transition: none;
    animation: none;
  }
  
}
</style>
