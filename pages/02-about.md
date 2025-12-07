---
transition: fade-out
layout: center
class: text-center
---

# 발표 계기

<div v-click v-motion
  :initial="{ opacity: 0, y: -20 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="text-2xl text-gray-400 mt-8 leading-relaxed">
워크샵 일정 짜다가 추천받아 진행하게...
</div>

<div v-click v-motion
  :initial="{ opacity: 0, y: -20 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="text-xl text-gray-200 mt-6 leading-relaxed max-w-2xl mx-auto">
이번 겨울 휴가때 갔던 <span v-mark.highlight.blue="3">도쿄와 쿠사츠 여행을 준비했던 경험</span>을 바탕으로<br>
<span v-mark.highlight.orange="4">저의 일본 여행하는 방식을 소개</span>하고, 쿠사츠 여행기를 말해보겠습니다.
</div>

---
transition: slide-up
layout: two-cols-header
---

# 발표자 소개 TMI

::left::

<div v-click v-motion
  :initial="{ opacity: 0, y: -20 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }">

### <mdi-account-tie class="inline text-blue-400" /> 발표자 정보
- 개발팀의 프론트엔드를 주로 개발하는 윤준현
- 군대에서 **8년전**에 딴 JLPT **N1**, **N2** 자격 소지
- 일본 **술**과 **온천**을 좋아합니다 <mdi-glass-wine class="inline" /><mdi-hot-tub class="inline" />

</div>

<div class="mt-6" v-click="2" v-motion
  :initial="{ opacity: 0, y: -20 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }">

### <mdi-flag-variant class="inline text-red-400" /> 주요 일본 여행 경력

<div v-click="2" v-motion
  :initial="{ opacity: 0, y: -15 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 300 } }">

- 첫 일본 여행: **2015년** 큐슈 3박 5일 패키지 여행 - <mdi-ferry class="inline" />

</div>

<div v-click="3" v-motion
  :initial="{ opacity: 0, y: -15 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 300 } }"
  style="margin-top: -16px">

- 나고야 대학 **교환학생** (약 1년)

</div>

<div v-click="4" v-motion
  :initial="{ opacity: 0, y: -15 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 300 } }"
  style="margin-top: -16px">

- **27일간 일본 일주** - 오카야마 ~ 아오모리

</div>

<div v-click="5" v-motion
  :initial="{ opacity: 0, y: -15 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 300 } }"
  style="margin-top: -16px">

- 그 외 10회 이상

</div>

</div>

::right::

<div v-click="3" v-motion
  :initial="{ opacity: 0, y: -30 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }">

<img src="/assets/CleanShot_2025-12-07_16.52.31@2x.png" class="h-48 rounded-lg shadow-lg mx-auto border border-gray-700" />

</div>

<div v-click="4" v-motion
  :initial="{ opacity: 0, y: -30 }"
  :enter="{ opacity: 1, y: 0, transition: { duration: 400 } }"
  class="mt-2">

<img src="/assets/image-20251207165909131-5094351.png" class="h-60 rounded-lg shadow-lg mx-auto border border-gray-700" />

</div>
