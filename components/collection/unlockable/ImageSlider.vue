<template>
  <div class="unlockable-image-slider mt-6">
    <div ref="container" class="keen-slider">
      <img
        v-for="image in imageList"
        :key="image"
        :src="image"
        class="keen-slider__slide" />
    </div>

    <Transition name="fade">
      <div
        v-if="sliderSettings.leftArrowValid"
        class="arrow arrow-left"
        @click="slider?.moveToIdx(sliderSettings.leftCarouselIndex)"></div>
    </Transition>
    <Transition name="fade">
      <div
        v-if="sliderSettings.rightArrowValid"
        class="arrow arrow-right"
        @click="slider?.moveToIdx(sliderSettings.rightCarouselIndex)"></div>
    </Transition>
    <div ref="thumbnail" class="keen-slider thumbnail">
      <div v-for="image in imageList" :key="image" class="keen-slider__slide">
        <img :src="image" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useKeenSlider } from 'keen-slider/vue.es'
import 'keen-slider/keen-slider.min.css'
const emit = defineEmits(['select'])

const props = defineProps<{
  imageList: string[]
}>()

function ThumbnailPlugin(main) {
  return (slider) => {
    function removeActive() {
      slider.slides.forEach((slide) => {
        slide.classList.remove('active')
      })
    }
    function addActive(idx) {
      slider.slides[idx].classList.add('active')
    }

    function addClickEvents() {
      slider.slides.forEach((slide, idx) => {
        slide.addEventListener('click', () => {
          main.value.moveToIdx(idx)
          emit('select', props.imageList[idx])
        })
      })
    }

    slider.on('created', () => {
      addActive(slider.track.details.rel)
      addClickEvents()
      main.value.on('animationStarted', () => {
        removeActive()
        const next = main.value.animator.targetIdx || 0
        addActive(main.value.track.absToRel(next))
        slider.moveToIdx(Math.min(slider.track.details.maxIdx, next))
      })
    })
  }
}

const [container, slider] = useKeenSlider({})
const [thumbnail] = useKeenSlider(
  {
    initial: 0,
    slides: {
      perView: 4,
      spacing: 1,
    },
  },
  [ThumbnailPlugin(slider)]
)

const sliderSettings = computed(() => {
  if (slider.value) {
    const { track, slides } = slider.value
    const abs = Number(track.details.abs)
    const perView = Number(4)
    const leftArrowValid = abs !== 0
    const rightArrowValid = abs + perView < slides.length
    const leftCarouselIndex = Math.max(abs - 1, 0)
    const rightCarouselIndex = Math.min(abs + 1, slides.length - perView)

    return {
      leftArrowValid,
      rightArrowValid,
      leftCarouselIndex,
      rightCarouselIndex,
    }
  } else {
    return {}
  }
})
</script>

<style scoped lang="scss">
@import '@/styles/abstracts/variables';

.unlockable-image-slider {
  width: 580px;

  @include mobile {
    width: 100%;
    max-width: 560px;
  }
  @include tablet-only {
    width: 768px;
  }
  img {
    @include ktheme() {
      border: 1px solid theme('border-color');
    }
  }
  .keen-slider__slide {
    height: 580px;
    width: 580px;
    object-fit: cover;
    @include tablet-only {
      width: 768px;
    }
    @include mobile {
      width: 100%;
    }
  }
  .thumbnail .keen-slider__slide {
    margin-top: 10px;
    height: 136px;
    width: 136px;
    @include tablet-only {
      width: calc(768px / 4);
    }
    @include mobile {
      width: 25%;
      max-width: 136px;
    }
    img {
      height: 136px;
      width: 136px;
      @include touch {
        width: calc(100% - 8px);
      }
      object-fit: cover;
      &:hover {
        opacity: 0.8;
      }
    }
    &.active {
      img {
        @include ktheme() {
          border: 3px solid theme('k-blue');
        }
      }
    }
  }
}
</style>
