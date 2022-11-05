<template>
  <v-app>
    <v-main>
      <audio
        ref="sfxChange"
        :src="SOUND_CHANGE"
        :muted="sfxMuted"
      />
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
            <div class="d-flex align-center">
              <v-btn
                icon
                disabled=""
                variant="plain"
              >
                <v-icon icon="mdi-clock" />
              </v-btn>
              <v-spacer />
              <v-btn
                icon
                variant="plain"
                :disabled="currentPose <= 0"
                @click="currentPose -= 1"
              >
                <v-icon icon="mdi-chevron-left" />
              </v-btn>
              <v-btn
                size="x-large"
                variant="tonal"
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
                variant="plain"
                :disabled="currentPose >= NUM_POSES - 1"
                @click="currentPose += 1"
              >
                <v-icon icon="mdi-chevron-right" />
              </v-btn>
              <v-spacer />
              <v-btn
                icon
                variant="plain"
                @click="sfxMuted = !sfxMuted"
              >
                <v-icon v-if="sfxMuted" icon="mdi-volume-off" />
                <v-icon v-else icon="mdi-volume-high" />
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

const config = useRuntimeConfig()

const NUM_POSES = Number(config.public.numPoses)
const SOUND_CHANGE = config.public.sound.change
const MAX_TIME = 31000 // ms
const LOOP_TIME = 100 // ms

const poses = [...Array(NUM_POSES)].map((value, key) => `/poses/${key + 1}.jpg`)
const sfxChange = ref(null)

let currentPose = ref(0)
let currentTime = ref(0.0)
let running = ref(true)
let sfxMuted = ref(false)

watch(currentPose, () => {
  currentTime.value = 0.0
})

const onLoopCicle = () => {
  if (!running.value) return

  currentTime.value += LOOP_TIME

  if (currentTime.value > MAX_TIME) {
    currentPose.value += 1
    running.value = currentPose.value < NUM_POSES
    sfxChange.value.play()
  }
}

setInterval(onLoopCicle, LOOP_TIME)
</script>
