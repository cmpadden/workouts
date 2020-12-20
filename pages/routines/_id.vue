<template>
  <div>
    <div :class="bg_color" class="h-screen p-4">
      <div
        v-if="completed"
        class="max-w-xl mx-auto rounded-lg shadow-md bg-green-200 p-4 mb-2"
      >
        <div class="text-xl text-bold text-center text-green-600">
          Congrats! You've completed the routine!
        </div>
      </div>

      <div class="max-w-xl mx-auto shadow-md">
        <div class="bg-gray-200 border-b-2 border-gray-500">
          <!-- Routine Title -->
          <div class="text-lg text-center font-bold uppercase text-gray-600">
            {{ routine.name }}
          </div>

          <!-- Routine Button Controls -->
          <div class="py-4 flex">
            <div
              class="w-20 flex-1 flex items-center justify-center font-semibold uppercase cursor-pointer"
              type="button"
              @click="back"
            >
              <svg
                class="h-4 w-4 ml-2"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M15 19l-7-7 7-7"
                />
              </svg>
              Back
            </div>
            <div
              v-if="this.timer === null"
              class="h-10 w-20 flex-1 flex items-center justify-center border-2 border-green-600 text-green-600 font-semibold uppercase cursor-pointer"
              type="button"
              @click="start"
            >
              Start
            </div>
            <div
              v-else
              class="h-10 w-20 flex-1 flex items-center justify-center bg-purple-600 text-white font-semibold uppercase cursor-pointer"
              type="button"
              @click="pause"
            >
              Pause
            </div>
            <div
              class="w-20 flex-1 flex items-center justify-center font-semibold uppercase cursor-pointer"
              type="button"
              @click="next(true)"
            >
              Skip
              <svg
                class="h-4 w-4 ml-2"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M9 5l7 7-7 7"
                />
              </svg>
            </div>
          </div>
          <template v-if="!routine_start">
            <div class="m-2">
              <div
                class="text-center text-lg sm:text-5xl font-semibold leading-tight"
                :class="{ 'text-red-600': seconds < 5 }"
              >
                {{ seconds }} seconds
              </div>
              <div class="text-center text-sm uppercase text-gray-600">
                {{ exercises_remaining }} exercises remaining
              </div>
            </div>
          </template>
        </div>

        <div class="max-w-xl mx-auto bg-white mb-2">
          <div>
            <div class="text-lg text-center font-bold uppercase text-gray-600">
              Exercise:
            </div>
            <template v-if="rest">
              <div class="text-5xl text-center uppercase">Rest...</div>
            </template>
            <template v-else>
              <div class="text-5xl text-center font-bold uppercase">
                {{ current_exercise }}
              </div>
              <!-- <div class="p-2"> -->
              <!--   <img -->
              <!--     class="object-contain h-40 w-full" -->
              <!--     src="https://via.placeholder.com/500" -->
              <!--   ></img> -->
              <!-- </div> -->
            </template>
          </div>
          <div class="">
            <div class="text-lg text-center font-bold uppercase text-gray-600">
              Coming Up
            </div>
            <div class="flex justify-center">
              <div v-for="e in exercise_window" :key="e.name" class="m-2 p-2">
                <div class="uppercase text-gray-600 text-sm text-center">
                  {{ e.name }} ({{ e.duration }}s)
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData({ params, $content }) {
    const routine = await $content("routines")
      .where({ slug: params.id })
      .fetch()
      .then((r) => r[0]);
    return { routine };
  },
  data() {
    return {
      routine_start: true, // indicates that we are at the start of the routine
      completed: false,
      exercise_ix: 0,
      seconds: 0,
      timer: null,
      rest: false,
      rest_duration: 15,
      bg_color: "bg-white",
    };
  },
  computed: {
    current_exercise() {
      return this.routine.exercises[this.exercise_ix].name;
    },
    exercises_remaining() {
      return this.routine.exercises.length - this.exercise_ix - 1;
    },
    exercise_window() {
      const remaining = this.routine.exercises.slice(
        this.exercise_ix + 1,
        this.routine.exercises.length
      );
      if (remaining.length < 3) {
        return remaining;
      } else {
        return remaining.slice(0, 3);
      }
    },
  },
  methods: {
    activate_timer() {
      this.timer = setInterval(() => this.countdown(), 10);
    },
    deactivate_timer() {
      clearInterval(this.timer);
      this.timer = null;
    },
    start() {
      // initialize exercise if just starting
      if (this.routine_start) {
        this.seconds = this.routine.exercises[this.exercise_ix].duration;
        this.completed = false;
        this.routine_start = false;
      }
      this.bg_color = "bg-green-200";
      this.activate_timer();
    },
    pause() {
      this.bg_color = "bg-blue-200";
      this.deactivate_timer();
    },
    reset() {
      this.deactivate_timer();
      this.exercise_ix = 0;
      this.routine_start = true;
      this.rest = false;
    },
    back() {
      if (this.exercise_ix > 0) {
        this.exercise_ix -= 1;
        this.seconds = this.routine.exercises[this.exercise_ix].duration;
      }
    },
    next(skip_rest = false) {
      // do not exceed the number of exercises in routine, instead loop back
      // to the beginning, and re-initialize the variables
      if (this.exercise_ix + 1 === this.routine.exercises.length) {
        this.completed = true;
        this.bg_color = "bg-white";
        this.reset();
        return;
      }

      // If we have not yet completed the routine, the load the next
      // exercise, or rest period -- alternative between the two
      if (this.rest || skip_rest) {
        this.rest = false;
        this.exercise_ix += 1;
        this.seconds = this.routine.exercises[this.exercise_ix].duration;
      } else {
        this.rest = true;
        this.seconds = this.rest_duration;
      }
    },
    countdown() {
      // Decrement the counter on callback, or if the counter has ended, then
      // progress to the next exercise or rest period
      if (this.seconds > 0) {
        this.seconds--;
      } else {
        this.next();
      }
    },
  },
};
</script>

