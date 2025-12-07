---
layout: section
transition: zoom
---

# <mdi-map-search class="inline" /> 여행지 물색

<div class="text-xl text-gray-400 mt-4">
일본 47개 도도부현을 모두 가보는 것이 목표!
</div>

---
transition: slide-left
---

# 여행지 물색

<div class="grid grid-cols-2 gap-8">

<div>

### <mdi-target class="inline text-red-400" /> 나의 방식

<div v-click v-motion
  :initial="{ opacity: 0, y: -20 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="mt-4 p-4 bg-gray-800/50 rounded-lg border border-gray-700" style="padding-bottom: 0">

- **구글 맵**에서 심심하면 주요 관광 스팟 찾아보기
  - 가보고 싶은 곳으로 **저장**해두기
</div>

<div v-click v-motion
  :initial="{ opacity: 0, y: -20 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="mt-4 p-4 bg-gray-800/50 rounded-lg border border-gray-700" style="padding-bottom: 0">

- 국내 또는 일본의 **여행 유튜브** 시청
  - 끌리는 곳을 구글 맵에 저장
</div>

<div v-click v-motion
  :initial="{ opacity: 0, y: -20 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="mt-4 p-4 bg-gray-800/50 rounded-lg border border-gray-700" style="padding-bottom: 0">

- 애니메이션에 나왔던 장소 가보기
</div>

</div>

<div v-click v-motion
  :initial="{ opacity: 0, y: -30 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="mt-12">

<img src="/assets/CleanShot_2025-12-07_15.46.04@2x.png" class="rounded-lg shadow-xl border border-gray-700" />

<div class="text-sm text-gray-500 mt-2 text-center">
구글 맵에 저장해둔 장소들
</div>

</div>

</div>
