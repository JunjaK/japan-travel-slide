<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  images: {
    type: Array,
    default: () => [
      { src: '/assets/0806EC42-2411-44A1-985B-DA3473620576_1_105_c.jpeg', title: 'Image 1', subtitle: 'Description 1' },
      { src: '/assets/093928DA-0E79-442F-A1B7-26C0407A4A7A_1_105_c.jpeg', title: 'Image 2', subtitle: 'Description 2' },
      { src: '/assets/sample3.jpg', title: 'Image 3', subtitle: 'Description 3' },
    ]
  },
  autoPlay: {
    type: Boolean,
    default: true
  },
  interval: {
    type: Number,
    default: 3000
  },
  cardWidth: {
    type: Number,
    default: 200
  },
  cardHeight: {
    type: Number,
    default: 280
  }
})

const rotation = ref(0)
const isHovered = ref(false)
let animationId = null
let lastTime = 0

const cardCount = computed(() => props.images.length)
const anglePerCard = computed(() => 360 / cardCount.value)
const radius = computed(() => props.cardWidth * 1.5)

const cardStyles = computed(() => {
  return props.images.map((_, index) => {
    const angle = anglePerCard.value * index + rotation.value
    const radian = (angle * Math.PI) / 180
    
    const x = Math.sin(radian) * radius.value
    const z = Math.cos(radian) * radius.value - radius.value
    const rotateY = -angle
    
    const scale = (z + radius.value * 2) / (radius.value * 2)
    const opacity = 0.4 + scale * 0.6
    const zIndex = Math.round(scale * 100)
    
    return {
      transform: `translateX(${x}px) translateZ(${z}px) rotateY(${rotateY}deg)`,
      opacity: opacity,
      zIndex: zIndex,
      filter: `blur(${(1 - scale) * 2}px)`
    }
  })
})

const animate = (currentTime) => {
  if (!lastTime) lastTime = currentTime
  const delta = currentTime - lastTime
  
  if (props.autoPlay && !isHovered.value) {
    rotation.value += delta * 0.02
  }
  
  lastTime = currentTime
  animationId = requestAnimationFrame(animate)
}

const handleMouseEnter = () => {
  isHovered.value = true
}

const handleMouseLeave = () => {
  isHovered.value = false
}

const rotateLeft = () => {
  rotation.value -= anglePerCard.value
}

const rotateRight = () => {
  rotation.value += anglePerCard.value
}

onMounted(() => {
  animationId = requestAnimationFrame(animate)
})

onUnmounted(() => {
  if (animationId) {
    cancelAnimationFrame(animationId)
  }
})
</script>

<template>
  <div class="carousel-container" @mouseenter="handleMouseEnter" @mouseleave="handleMouseLeave">
    <div class="carousel-scene">
      <div class="carousel-track">
        <div
          v-for="(image, index) in images"
          :key="index"
          class="carousel-card"
          :style="{
            ...cardStyles[index],
            width: `${cardWidth}px`,
            height: `${cardHeight}px`
          }"
        >
          <div class="card-inner">
            <img :src="image.src" :alt="image.title" class="card-image" />
            <div class="card-overlay">
              <h3 class="card-title">{{ image.title }}</h3>
              <p class="card-subtitle">{{ image.subtitle }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <div class="carousel-controls">
      <button class="control-btn" @click="rotateLeft">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
          <path d="M15.41 7.41L14 6l-6 6 6 6 1.41-1.41L10.83 12z"/>
        </svg>
      </button>
      <button class="control-btn" @click="rotateRight">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
          <path d="M10 6L8.59 7.41 13.17 12l-4.58 4.59L10 18l6-6z"/>
        </svg>
      </button>
    </div>
  </div>
</template>

<style scoped>
.carousel-container {
  position: relative;
  width: 100%;
  height: 400px;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.carousel-scene {
  perspective: 1000px;
  perspective-origin: center center;
}

.carousel-track {
  position: relative;
  width: 200px;
  height: 280px;
  transform-style: preserve-3d;
}

.carousel-card {
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -100px;
  margin-top: -140px;
  border-radius: 16px;
  overflow: hidden;
  transition: opacity 0.3s ease, filter 0.3s ease;
  transform-style: preserve-3d;
  backface-visibility: hidden;
}

.card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: 16px;
  overflow: hidden;
  background: linear-gradient(135deg, rgba(255,255,255,0.1) 0%, rgba(255,255,255,0.05) 100%);
  border: 1px solid rgba(255,255,255,0.2);
  box-shadow: 
    0 8px 32px rgba(0,0,0,0.3),
    inset 0 1px 0 rgba(255,255,255,0.2),
    0 0 0 1px rgba(255,255,255,0.1);
  backdrop-filter: blur(4px);
}

.card-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.card-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 16px;
  background: linear-gradient(to top, rgba(0,0,0,0.8) 0%, transparent 100%);
}

.card-title {
  margin: 0;
  font-size: 14px;
  font-weight: 600;
  color: white;
  text-shadow: 0 1px 2px rgba(0,0,0,0.5);
}

.card-subtitle {
  margin: 4px 0 0;
  font-size: 11px;
  color: rgba(255,255,255,0.7);
  text-shadow: 0 1px 2px rgba(0,0,0,0.5);
}

.carousel-controls {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 12px;
}

.control-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 1px solid rgba(255,255,255,0.2);
  background: rgba(255,255,255,0.1);
  color: white;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
  backdrop-filter: blur(4px);
}

.control-btn:hover {
  background: rgba(255,255,255,0.2);
  border-color: rgba(255,255,255,0.4);
  transform: scale(1.1);
}
</style>
