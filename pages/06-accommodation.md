---
layout: section
transition: zoom
---

# <mdi-bed class="inline" /> 숙소 예약

<div class="text-xl text-gray-400 mt-4">
호텔 예약 플랫폼 및 공식 홈페이지
</div>

---
transition: slide-left
---

# 숙소 예약

<div class="grid grid-cols-2 gap-8">

<div>

### <mdi-cellphone class="inline text-blue-400" /> Booking 등 호텔 예약 플랫폼

<div v-click v-motion
  :initial="{ opacity: 0, y: -20 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="mt-4 p-4 bg-gray-800/50 rounded-lg border border-gray-700">

**신주쿠 숙소**

- 인당 1박 **4천엔**
- 가부키쵸 근방 <span class="text-yellow-500">(<mdi-alert class="inline" /> 치안은 안좋음...)</span>
- 에어비앤비랑 비슷한 스타일

</div>

<div v-click v-motion
  :initial="{ opacity: 0, y: -30 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="mt-4">

<img src="/images/CleanShot_2025-12-07_16.22.01@2x.png" class="rounded-lg shadow-lg border border-gray-700" />

</div>

</div>

<div>

### <mdi-domain class="inline text-amber-400" /> 숙소 공식 홈페이지

<div v-click v-motion
  :initial="{ opacity: 0, y: -20 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="mt-4 p-4 bg-gray-800/50 rounded-lg border border-gray-700">

**카네미도리(金みどり)**: 쿠사츠 온천 여관

- **8월 13일**에 예약 (플랫폼에서 매진 상태)
- 식사 없이 인당 2박 특가로 <span class="text-green-400 font-bold">27,800엔</span>
- <mdi-star-four-points class="inline text-yellow-400" /> 쿠사츠 갈 일 있으면 이 숙소 추천!
</div>

<div v-click v-motion
  :initial="{ opacity: 0, y: -30 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="mt-4">

<img src="/images/CleanShot_2025-12-07_16.25.35@2x.png" class="rounded-lg shadow-lg border border-gray-700" />

</div>

</div>

</div>
