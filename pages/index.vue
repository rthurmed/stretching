<template>
  <v-app>
    <v-main>
      <v-container>
        <v-window
          :show-arrows="false"
          :model-value="currentPose"
        >
          <v-window-item
            v-for="pose in poses"
            :key="pose"
          >
            <v-card
              :image="pose"
              height="calc(100vh - 200px)"
            />
          </v-window-item>
        </v-window>
        <v-card class="mt-4">
          <template v-slot:loader>
            <v-progress-linear
              color="deep-purple"
              height="8"
              :model-value="currentTime"
              :max="MAX_TIME"
            ></v-progress-linear>
          </template>
          <template v-slot:text>
            <div class="text-center mb-3">
              {{ currentPose + 1 }} / {{ NUM_POSES }}
            </div>
            <div class="d-flex justify-center align-center">
              <v-btn
                icon
                :disabled="currentPose <= 0"
                @click="currentPose -= 1"
              >
                <v-icon icon="mdi-chevron-left" />
              </v-btn>
              <v-btn
                size="x-large"
                stacked
                rounded
                icon
                class="mx-4"
                @click="running = !running"
              >
                <v-icon :icon="running ? 'mdi-pause' : 'mdi-play'" />
              </v-btn>
              <v-btn
                icon
                :disabled="currentPose >= NUM_POSES - 1"
                @click="currentPose += 1"
              >
                <v-icon icon="mdi-chevron-right" />
              </v-btn>
            </div>
          </template>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, watch } from 'vue'

useHead({
  title: "Stretching"
})

const NUM_POSES = 18
const MAX_TIME = 31000 // ms
const LOOP_TIME = 100 // ms

// TODO: play noise when move to next slide

const poses = [...Array(NUM_POSES)].map((value, key) => `/poses/${key + 1}.jpg`)
let currentPose = ref(0)
let currentTime = ref(0.0)
let running = ref(true)

watch(currentPose, () => {
  currentTime.value = 0.0
})

const onLoopCicle = () => {
  if (!running.value) return

  currentTime.value += LOOP_TIME

  if (currentTime.value > MAX_TIME) {
    currentPose.value += 1
    running.value = currentPose.value < NUM_POSES
  }
}

setInterval(onLoopCicle, LOOP_TIME)
</script>
