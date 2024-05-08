<template>
  <span class="font-3 text-color-1"> TEXT CAROUSEL </span>
  <div class="flex flex-col gap-4">
    <text-carousel
      :animation-direction="ETextCarouselDirection.UP"
      :item-list="textCarouselItemList"
      :container-height="INFO_BANNER_ELEMENT_HEIGHT"
      :animation-interval="INFO_BANNER_ANIMATION_INTERVAL"
      :animation-duration="INFO_BANNER_ANIMATION_DURATION"
    />
  </div>
</template>

<script lang="ts" setup>
import TextCarousel from '@/components/text-carousel/text-carousel.vue';

import {
  ETextCarouselDirection,
} from '@/components/text-carousel/text-carousel';

const INFO_BANNER_ELEMENT_HEIGHT = 20;
const INFO_BANNER_ANIMATION_INTERVAL = 4000;
const INFO_BANNER_ANIMATION_DURATION = 1000;

const textCarouselItemList = [
  'First Message',
  'Second Message',
];
</script>
