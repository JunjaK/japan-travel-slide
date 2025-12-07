<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue'

const props = defineProps({
  title: {
    type: [String, Array],
    default: 'Gallery'
  },
  subtitle: {
    type: String,
    default: ''
  },
  images: {
    type: Array,
    default: () => []
  },
  autoRotate: {
    type: Boolean,
    default: true
  },
  rotationSpeed: {
    type: Number,
    default: 0.3
  },
  size: {
    type: String,
    default: 'medium',
    validator: (value) => ['small', 'medium', 'large'].includes(value)
  }
})

const rotation = ref(0)
const isHovered = ref(false)
const hoveredIndex = ref(-1)
const showModal = ref(false)
const showFullscreen = ref(false)
const fullscreenImage = ref(null)
const fullscreenIndex = ref(-1)
const modalHoveredIndex = ref(-1)

let animationId = null
let lastTime = 0

// 이미지 경로 정규화 함수 (빌드 시 base path 처리)
const normalizeImagePath = (path) => {
  if (!path) return ''
  // 이미 절대 경로로 시작하는 경우 그대로 반환
  if (path.startsWith('http://') || path.startsWith('https://')) {
    return path
  }
  
  // 이미 /로 시작하는 경우 그대로 반환
  if (path.startsWith('/')) {
    return path
  }
  // 상대 경로인 경우 base URL 추가
  const base = import.meta.env.BASE_URL || '/'
  return `${base}${path.startsWith('./') ? path.slice(2) : path}`
}

// 이미지 경로를 정규화한 computed
const normalizedImages = computed(() => {
  return props.images.map(img => ({
    ...img,
    src: normalizeImagePath(img.src)
  }))
})

// 최대 8개의 이미지만 표시
const displayImages = computed(() => normalizedImages.value.slice(0, 8))
const imageCount = computed(() => displayImages.value.length)
const anglePerImage = computed(() => 360 / Math.max(imageCount.value, 1))

const imageStyles = computed(() => {
  const radius = props.size === 'small' ? 110 : 140
  return displayImages.value.map((_, index) => {
    const baseAngle = anglePerImage.value * index + rotation.value
    const radian = (baseAngle * Math.PI) / 180
    
    const x = Math.cos(radian) * radius
    const y = Math.sin(radian) * radius
    
    const isHoveredItem = hoveredIndex.value === index
    const scale = isHoveredItem ? 1.3 : 1
    const zIndex = isHoveredItem ? 100 : 10
    const rotateZ = baseAngle + 90
    
    return {
      transform: `translate(${x}px, ${y}px) rotate(${rotateZ}deg) scale(${scale})`,
      zIndex,
      transition: 'transform 0.3s ease, box-shadow 0.3s ease'
    }
  })
})

const animate = (currentTime) => {
  if (!lastTime) lastTime = currentTime
  const delta = currentTime - lastTime
  
  if (props.autoRotate && !isHovered.value && !showModal.value) {
    rotation.value += delta * props.rotationSpeed * 0.01
  }
  
  lastTime = currentTime
  animationId = requestAnimationFrame(animate)
}

const handleMouseEnter = () => {
  isHovered.value = true
}

const handleMouseLeave = () => {
  isHovered.value = false
  hoveredIndex.value = -1
}

const handleImageHover = (index) => {
  hoveredIndex.value = index
}

const handleImageLeave = () => {
  hoveredIndex.value = -1
}

const openModal = () => {
  showModal.value = true
}

const closeModal = () => {
  showModal.value = false
  modalHoveredIndex.value = -1
}

const openFullscreen = (image, index) => {
  fullscreenImage.value = image
  fullscreenIndex.value = index
  showFullscreen.value = true
}

const closeFullscreen = () => {
  showFullscreen.value = false
  fullscreenImage.value = null
  fullscreenIndex.value = -1
}

const goToPrevImage = () => {
  if (fullscreenIndex.value > 0) {
    fullscreenIndex.value--
    fullscreenImage.value = normalizedImages.value[fullscreenIndex.value]
  } else {
    // Loop to last image
    fullscreenIndex.value = normalizedImages.value.length - 1
    fullscreenImage.value = normalizedImages.value[fullscreenIndex.value]
  }
}

const goToNextImage = () => {
  if (fullscreenIndex.value < normalizedImages.value.length - 1) {
    fullscreenIndex.value++
    fullscreenImage.value = normalizedImages.value[fullscreenIndex.value]
  } else {
    // Loop to first image
    fullscreenIndex.value = 0
    fullscreenImage.value = normalizedImages.value[fullscreenIndex.value]
  }
}

const handleModalImageHover = (index) => {
  modalHoveredIndex.value = index
}

const handleModalImageLeave = () => {
  modalHoveredIndex.value = -1
}

const handleKeydown = (e) => {
  // 모달이나 전체화면이 열려있을 때 Slidev 키보드 이벤트 차단
  if (showModal.value || showFullscreen.value) {
    e.stopPropagation()
    
    if (e.key === 'Escape') {
      e.preventDefault()
      if (showFullscreen.value) {
        closeFullscreen()
      } else if (showModal.value) {
        closeModal()
      }
    }
    
    // 전체화면에서 방향키로 이미지 이동
    if (showFullscreen.value) {
      if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') {
        e.preventDefault()
        goToPrevImage()
      } else if (e.key === 'ArrowRight' || e.key === 'ArrowDown' || e.key === ' ') {
        e.preventDefault()
        goToNextImage()
      }
    }
  }
}

onMounted(() => {
  animationId = requestAnimationFrame(animate)
  // capture: true로 Slidev보다 먼저 이벤트 처리
  window.addEventListener('keydown', handleKeydown, { capture: true })
})

onUnmounted(() => {
  if (animationId) {
    cancelAnimationFrame(animationId)
  }
  window.removeEventListener('keydown', handleKeydown, { capture: true })
})
</script>

<template>
  <div class="kaleidoscope-container">
    <!-- Main Carousel -->
    <div 
      :class="['kaleidoscope-wheel', `size-${size}`]"
      @mouseenter="handleMouseEnter"
      @mouseleave="handleMouseLeave"
      @click="openModal"
    >
      <!-- Center Title -->
      <div class="center-content">
        <h2 class="center-title">
          <template v-if="Array.isArray(title)">
            <span v-for="(line, idx) in title" :key="idx">{{ line }}<br v-if="idx < title.length - 1" /></span>
          </template>
          <template v-else>{{ title }}</template>
        </h2>
        <p v-if="subtitle" class="center-subtitle">{{ subtitle }}</p>
        <div class="click-hint">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="currentColor">
            <path d="M12 8l-6 6 1.41 1.41L12 10.83l4.59 4.58L18 14z"/>
          </svg>
          <span>Click to explore</span>
        </div>
      </div>
      
      <!-- Rotating Images -->
      <div class="images-orbit">
        <div
          v-for="(image, index) in displayImages"
          :key="index"
          class="orbit-image"
          :style="imageStyles[index]"
          @mouseenter="handleImageHover(index)"
          @mouseleave="handleImageLeave"
        >
          <img :src="image.src" :alt="image.title || `Image ${index + 1}`" />
          <div class="image-glow"></div>
        </div>
      </div>
      
      <!-- Decorative Ring -->
      <div class="decorative-ring"></div>
      <div class="decorative-ring ring-2"></div>
    </div>

    <!-- Modal -->
    <Teleport to="body">
      <Transition name="modal">
        <div v-if="showModal" class="modal-overlay" @click.self="closeModal">
          <div class="modal-content">
            <button class="modal-close" @click="closeModal">
              <svg width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
                <path d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z"/>
              </svg>
            </button>
            
            <h2 class="modal-title">
              <template v-if="Array.isArray(title)">
                <span v-for="(line, idx) in title" :key="idx">{{ line }}<br v-if="idx < title.length - 1" /></span>
              </template>
              <template v-else>{{ title }}</template>
            </h2>
            <p v-if="subtitle" class="modal-subtitle">{{ subtitle }}</p>
            
            <div class="modal-grid">
              <div
                v-for="(image, index) in normalizedImages"
                :key="index"
                class="modal-image-card"
                :class="{ 'is-hovered': modalHoveredIndex === index }"
                @mouseenter="handleModalImageHover(index)"
                @mouseleave="handleModalImageLeave"
                @click="openFullscreen(image, index)"
              >
                <div class="card-3d-wrapper">
                  <div class="card-3d-inner">
                    <img :src="image.src" :alt="image.tags?.join(', ') || `Image ${index + 1}`" />
                    <div class="card-overlay" v-if="image.tags?.length">
                      <div class="card-tags">
                        <span v-for="(tag, tagIndex) in image.tags.slice(0, 3)" :key="tagIndex" class="tag">{{ tag }}</span>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </Transition>
    </Teleport>

    <!-- Fullscreen View -->
    <Teleport to="body">
      <Transition name="fullscreen">
        <div v-if="showFullscreen" class="fullscreen-overlay" @click="closeFullscreen">
          <!-- Previous Button -->
          <button class="nav-btn nav-prev" @click.stop="goToPrevImage">
            <svg width="32" height="32" viewBox="0 0 24 24" fill="currentColor">
              <path d="M15.41 7.41L14 6l-6 6 6 6 1.41-1.41L10.83 12z"/>
            </svg>
          </button>
          
          <img 
            :src="fullscreenImage?.src" 
            :alt="fullscreenImage?.tags?.join(', ')" 
            class="fullscreen-image"
            @click.stop
          />
          
          <!-- Next Button -->
          <button class="nav-btn nav-next" @click.stop="goToNextImage">
            <svg width="32" height="32" viewBox="0 0 24 24" fill="currentColor">
              <path d="M10 6L8.59 7.41 13.17 12l-4.58 4.59L10 18l6-6z"/>
            </svg>
          </button>
          
          <!-- Image Tags -->
          <div class="fullscreen-tags" v-if="fullscreenImage?.tags?.length" @click.stop>
            <span v-for="(tag, tagIndex) in fullscreenImage.tags" :key="tagIndex" class="fullscreen-tag">{{ tag }}</span>
          </div>
          
          <!-- Image Counter -->
          <div class="image-counter">
            {{ fullscreenIndex + 1 }} / {{ normalizedImages.length }}
          </div>
          
          <!-- Keyboard Hint -->
          <div class="keyboard-hint">
            <span><kbd>←</kbd> <kbd>→</kbd> 이동</span>
            <span><kbd>ESC</kbd> 닫기</span>
          </div>
          
          <button class="fullscreen-close" @click="closeFullscreen">
            <svg width="32" height="32" viewBox="0 0 24 24" fill="currentColor">
              <path d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z"/>
            </svg>
          </button>
        </div>
      </Transition>
    </Teleport>
  </div>
</template>

<style scoped>
.kaleidoscope-container {
  position: relative;
  width: 100%;
  height: 400px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.kaleidoscope-container:has(.size-small) {
  height: 320px;
}

.kaleidoscope-wheel {
  position: relative;
  width: 350px;
  height: 350px;
  cursor: pointer;
  transition: transform 0.3s ease;
}

.kaleidoscope-wheel:hover {
  transform: scale(1.02);
}

/* Small size for grid-cols-3 */
.kaleidoscope-wheel.size-small {
  width: 280px;
  height: 280px;
}

.kaleidoscope-wheel.size-small .center-title {
  font-size: 1.1rem;
  line-height: 1.2;
}

.kaleidoscope-wheel.size-small .center-subtitle {
  font-size: 0.75rem;
}

.kaleidoscope-wheel.size-small .click-hint {
  font-size: 0.65rem;
  margin-top: 4px;
}

.kaleidoscope-wheel.size-small .click-hint svg {
  width: 12px;
  height: 12px;
}

.kaleidoscope-wheel.size-small .orbit-image {
  width: 55px;
  height: 55px;
  margin: -27.5px;
}

.kaleidoscope-wheel.size-small .decorative-ring {
  width: 220px;
  height: 220px;
  margin: -110px;
}

.kaleidoscope-wheel.size-small .decorative-ring.ring-2 {
  width: 250px;
  height: 250px;
  margin: -125px;
}

.center-content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
  z-index: 50;
  pointer-events: none;
}

.center-title {
  font-size: 1.5rem;
  font-weight: 700;
  color: white;
  text-shadow: 0 2px 10px rgba(0,0,0,0.5);
  margin: 0;
  background: linear-gradient(135deg, #fff 0%, #a5b4fc 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.center-subtitle {
  font-size: 0.875rem;
  color: rgba(255,255,255,0.7);
  margin: 4px 0 0;
  text-shadow: 0 1px 4px rgba(0,0,0,0.5);
}

.click-hint {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  margin-top: 8px;
  font-size: 0.75rem;
  color: rgba(255,255,255,0.5);
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 0.5; }
  50% { opacity: 1; }
}

.images-orbit {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 0;
  height: 0;
}

.orbit-image {
  position: absolute;
  width: 70px;
  height: 70px;
  margin: -35px;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 
    0 4px 20px rgba(0,0,0,0.4),
    0 0 30px rgba(99, 102, 241, 0.3);
  cursor: pointer;
}

.orbit-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.orbit-image:hover {
  box-shadow: 
    0 8px 40px rgba(0,0,0,0.5),
    0 0 50px rgba(99, 102, 241, 0.5);
}

.image-glow {
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, rgba(255,255,255,0.2) 0%, transparent 50%);
  pointer-events: none;
}

.decorative-ring {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 280px;
  height: 280px;
  margin: -140px;
  border: 1px solid rgba(255,255,255,0.1);
  border-radius: 50%;
  pointer-events: none;
}

.decorative-ring.ring-2 {
  width: 320px;
  height: 320px;
  margin: -160px;
  border-color: rgba(99, 102, 241, 0.2);
}

/* Modal Styles - Fullscreen */
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(5, 5, 15, 0.98);
  backdrop-filter: blur(20px);
  display: flex;
  flex-direction: column;
  z-index: 1000;
  overflow: hidden;
}

.modal-content {
  width: 100%;
  height: 100%;
  padding: 24px 40px;
  overflow-y: auto;
  position: relative;
}

.modal-content::-webkit-scrollbar {
  width: 8px;
}

.modal-content::-webkit-scrollbar-track {
  background: rgba(255,255,255,0.05);
}

.modal-content::-webkit-scrollbar-thumb {
  background: rgba(255,255,255,0.2);
  border-radius: 4px;
}

.modal-content::-webkit-scrollbar-thumb:hover {
  background: rgba(255,255,255,0.3);
}

.modal-close {
  position: fixed;
  top: 24px;
  right: 24px;
  background: rgba(255,255,255,0.1);
  border: 1px solid rgba(255,255,255,0.2);
  border-radius: 50%;
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: white;
  transition: all 0.2s ease;
  z-index: 100;
  backdrop-filter: blur(10px);
}

.modal-close:hover {
  background: rgba(255,255,255,0.2);
  transform: scale(1.1);
  border-color: rgba(255,255,255,0.4);
}

.modal-title {
  font-size: 2.5rem;
  font-weight: 700;
  color: white;
  margin: 0 0 8px;
  text-align: center;
  background: linear-gradient(135deg, #fff 0%, #a5b4fc 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.modal-subtitle {
  font-size: 1.125rem;
  color: rgba(255,255,255,0.6);
  margin: 0 0 32px;
  text-align: center;
}

.modal-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 20px;
  padding-bottom: 40px;
}

.modal-image-card {
  perspective: 1000px;
  cursor: pointer;
}

.card-3d-wrapper {
  position: relative;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(0,0,0,0.3);
  background: rgba(255,255,255,0.05);
  border: 1px solid rgba(255,255,255,0.1);
  transition: box-shadow 0.4s ease, border-color 0.4s ease, transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.modal-image-card.is-hovered .card-3d-wrapper {
  box-shadow: 
    0 30px 60px rgba(0,0,0,0.5),
    0 0 40px rgba(99, 102, 241, 0.4),
    inset 0 1px 0 rgba(255,255,255,0.2);
  border-color: rgba(99, 102, 241, 0.5);
  transform: translateY(-15px) scale(1.08);
}

.card-3d-inner {
  position: relative;
  width: 100%;
  height: 100%;
  transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  transform-style: preserve-3d;
  will-change: transform;
}

.modal-image-card.is-hovered .card-3d-inner {
  transform: rotateX(8deg) rotateY(-8deg);
}

.modal-image-card img {
  width: 100%;
  height: 180px;
  object-fit: cover;
  display: block;
  border-radius: 16px;
  transition: transform 0.4s ease;
}

.modal-image-card.is-hovered img {
  transform: scale(1.1);
}

.card-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 12px;
  background: linear-gradient(to top, rgba(0,0,0,0.9) 0%, rgba(0,0,0,0.5) 50%, transparent 100%);
  transform: translateY(100%);
  transition: transform 0.3s ease;
}

.modal-image-card.is-hovered .card-overlay {
  transform: translateY(0);
}

.card-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
}

.tag {
  display: inline-block;
  padding: 4px 10px;
  font-size: 0.75rem;
  font-weight: 500;
  color: white;
  background: rgba(99, 102, 241, 0.8);
  border-radius: 20px;
  backdrop-filter: blur(4px);
}

/* Fullscreen Styles */
.fullscreen-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.98);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  cursor: pointer;
}

.fullscreen-image {
  max-width: 90vw;
  max-height: 90vh;
  object-fit: contain;
  border-radius: 8px;
  box-shadow: 0 0 100px rgba(99, 102, 241, 0.3);
}

.fullscreen-tags {
  position: absolute;
  bottom: 80px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  max-width: 80vw;
}

.fullscreen-tag {
  display: inline-block;
  padding: 8px 20px;
  font-size: 0.9rem;
  font-weight: 500;
  color: white;
  background: rgba(99, 102, 241, 0.7);
  border-radius: 25px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255,255,255,0.2);
  transition: all 0.2s ease;
  cursor: default;
}

.fullscreen-tag:hover {
  background: rgba(99, 102, 241, 0.9);
  transform: scale(1.05);
}

.fullscreen-close {
  position: absolute;
  top: 20px;
  right: 20px;
  background: rgba(255,255,255,0.1);
  border: none;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: white;
  transition: all 0.2s ease;
}

.fullscreen-close:hover {
  background: rgba(255,255,255,0.2);
  transform: scale(1.1);
}

/* Navigation Buttons */
.nav-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(255,255,255,0.1);
  border: 1px solid rgba(255,255,255,0.2);
  border-radius: 50%;
  width: 60px;
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: white;
  transition: all 0.2s ease;
  backdrop-filter: blur(10px);
  z-index: 10;
}

.nav-btn:hover {
  background: rgba(255,255,255,0.2);
  border-color: rgba(255,255,255,0.4);
  transform: translateY(-50%) scale(1.1);
}

.nav-prev {
  left: 30px;
}

.nav-next {
  right: 30px;
}

/* Image Counter */
.image-counter {
  position: absolute;
  top: 24px;
  left: 50%;
  transform: translateX(-50%);
  padding: 8px 20px;
  background: rgba(0,0,0,0.6);
  border-radius: 20px;
  color: white;
  font-size: 0.9rem;
  font-weight: 500;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255,255,255,0.1);
}

/* Keyboard Hint */
.keyboard-hint {
  position: absolute;
  bottom: 24px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 20px;
  color: rgba(255,255,255,0.5);
  font-size: 0.8rem;
}

.keyboard-hint kbd {
  display: inline-block;
  padding: 2px 8px;
  background: rgba(255,255,255,0.1);
  border-radius: 4px;
  border: 1px solid rgba(255,255,255,0.2);
  font-family: inherit;
  font-size: 0.75rem;
}

/* Transitions */
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-active .modal-content,
.modal-leave-active .modal-content {
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.modal-enter-from .modal-content,
.modal-leave-to .modal-content {
  transform: scale(0.9);
  opacity: 0;
}

.fullscreen-enter-active,
.fullscreen-leave-active {
  transition: opacity 0.3s ease;
}

.fullscreen-enter-from,
.fullscreen-leave-to {
  opacity: 0;
}

.fullscreen-enter-active .fullscreen-image,
.fullscreen-leave-active .fullscreen-image {
  transition: transform 0.3s ease;
}

.fullscreen-enter-from .fullscreen-image,
.fullscreen-leave-to .fullscreen-image {
  transform: scale(0.8);
}
</style>

