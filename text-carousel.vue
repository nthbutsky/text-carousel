<template>
  <div
    class="overflow-hidden"
    :style="`height: ${containerHeight}px`"
  >
    <div ref="elementRef">
      <div
        v-for="(item, index) in itemList"
        :key="index"
        class="flex flex-col"
      >
        <div
          class="w-full overflow-hidden text-ellipsis whitespace-nowrap"
          :style="{height: `${containerHeight}px`}"
          v-bind="$attrs"
        >
          {{ item }}
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import {
  onMounted, onUnmounted, ref,
} from 'vue';

import {
  ETextCarouselDirection,
} from '@/components/text-carousel/text-carousel';

interface IProps {
  itemList: string[];
  containerHeight: number;
  animationInterval: number;
  animationDuration: number;
  animationDirection: ETextCarouselDirection;
}

const props = defineProps<IProps>();

const elementRef = ref<HTMLElement | null>(null);
const elements = ref<null | HTMLCollection>(null);
const elementLength = ref<number>(0);
const elementStartPosition = ref<number>(0);
const elementEndPosition = ref<number>(props.containerHeight);
const elementCurrentIndex = ref<number>(0);
const elementDirection = ref<ETextCarouselDirection>(props.animationDirection);
const animationIntervalId = ref<ReturnType<typeof setInterval>>();

const move = (direction: ETextCarouselDirection) => {
  let start;
  let end;

  if (direction === ETextCarouselDirection.UP) {
    start = elementStartPosition.value === 0 ? 0 : `-${elementStartPosition.value}px`;
    end = `-${elementEndPosition.value}px`;
  } else {
    start = `-${elementStartPosition.value}px`;
    end = elementEndPosition.value === 0 ? 0 : `-${elementEndPosition.value}px`;
  }

  if (elementRef.value) {
    elementRef.value.animate(
      [
        {
          transform: `translateY(${start})`,
        },
        {
          transform: `translateY(${end})`,
        },
      ],
      {
        duration: props.animationDuration,
        iterations: 1,
        fill: 'forwards',
        easing: 'ease-in-out',
      },
    );
  }
};

const shiftContainer = (direction: ETextCarouselDirection) => {
  if (direction === ETextCarouselDirection.UP) {
    move(ETextCarouselDirection.UP);

    if (elementCurrentIndex.value === elementLength.value - 1) {
      elementCurrentIndex.value = 0;
      elementStartPosition.value = 0;
      elementEndPosition.value = props.containerHeight;
    } else {
      elementCurrentIndex.value += 1;
      elementStartPosition.value += props.containerHeight;
      elementEndPosition.value += props.containerHeight;
    }
  } else {
    move(ETextCarouselDirection.DOWN);

    if (elementCurrentIndex.value === 0) {
      elementCurrentIndex.value = elementLength.value - 1;
      elementStartPosition.value = elementLength.value * props.containerHeight;
      elementEndPosition.value = elementStartPosition.value - props.containerHeight;
    } else {
      elementCurrentIndex.value -= 1;
      elementStartPosition.value -= props.containerHeight;
      elementEndPosition.value -= props.containerHeight;
    }
  }
};

const initCarousel = (itemList: string[]) => {
  if (itemList.length < 2) {
    return;
  }
  elements.value = elementRef.value!.children;
  elementLength.value = itemList.length;
  const firstCode = elements.value[0];
  const cloneFirst = firstCode.cloneNode(true);
  elementRef.value!.appendChild(cloneFirst);

  if (elementDirection.value === ETextCarouselDirection.DOWN) {
    elementCurrentIndex.value = elementLength.value - 1;
    elementStartPosition.value = elementLength.value * props.containerHeight;
    elementEndPosition.value = elementStartPosition.value - props.containerHeight;
  }
  animationIntervalId.value = setInterval(() => {
    shiftContainer(elementDirection.value);
  }, props.animationInterval);
};

onMounted(() => {
  initCarousel(props.itemList);
});

onUnmounted(() => {
  clearInterval(animationIntervalId.value);
});
</script>
