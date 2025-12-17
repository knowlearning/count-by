<template>
  <div>
    <h1>Count By {{ step }}</h1>

    <div class="problem-line">
      <template v-for="(item, index) in problem.items" :key="index">
        <NumberInput
          v-model="item.userInput"
          :locked="!item.missing"
          width="120px"
        />
        <span
          v-if="index !== problem.items.length - 1"
          style="padding:0 10px 0 2px; font-size: 2rem;"
        >,</span>
      </template>
    </div>

    <button @click="checkAnswer" style="margin-top: 20px;">Submit</button>
  </div>
</template>

<script setup>
import { ref, onBeforeMount } from 'vue'
import NumberInput from './components/NumberInput.vue'
import randomQuestion from './randomQuestion.js'

const step = ref(undefined)
const min = ref(undefined)
const max = ref(undefined)
const sequenceLength = ref(undefined)
const missingCount = ref(undefined)
const problem = ref(undefined)

const DEFAULT_STEP = 6
const DEFAULT_MIN = 1
const DEFAULT_MAX = 100
const DEFAULT_SEQUENCE_LENGTH = 6
const DEFAULT_MISSING_COUNT = 3

onBeforeMount(() => {
  const params = new URLSearchParams(window.location.search)

  const parseParam = (key, defaultValue, minAllowed) => {
    const val = params.get(key)
    if (val !== null) {
      const n = Number(val)
      if (Number.isFinite(n) && (minAllowed === undefined || n >= minAllowed)) {
        return n
      }
    }
    return defaultValue
  }

  step.value = parseParam('countBy', DEFAULT_STEP, 1)
  min.value  = parseParam('min', DEFAULT_MIN)
  max.value  = parseParam('max', DEFAULT_MAX, min.value)
  sequenceLength.value = parseParam('sequenceLength', DEFAULT_SEQUENCE_LENGTH)
  missingCount.value = parseParam('missingCount', DEFAULT_MISSING_COUNT)

  problem.value = randomQuestion(
    step.value,
    min.value,
    max.value,
    sequenceLength.value,
    missingCount.value
  )
  console.log(problem.value)
})

// Check the user's answers
function checkAnswer() {
  const allCorrect = problem.value.items.every(item => {
    return !item.missing || Number(item.userInput) === item.value
  })

  if (allCorrect) {
    alert('✅ Correct!')
  } else {
    alert('❌ Some answers are wrong. Try again!')
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.problem-line {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  gap: 8px;
}
</style>
