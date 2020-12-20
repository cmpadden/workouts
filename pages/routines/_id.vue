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
          <div class="flex justify-between justify-center">
            <div
              v-if="this.timer === null"
              class="rounded-full h-20 w-20 flex items-center justify-center bg-green-600 border-4 border-green-700 text-white font-semibold uppercase m-2 cursor-pointer"
              type="button"
              @click="start"
            >
              Start
            </div>
            <div
              v-else
              class="rounded-full h-20 w-20 flex items-center justify-center bg-purple-600 border-4 border-purple-900 text-white font-semibold uppercase m-2 cursor-pointer"
              type="button"
              @click="pause"
            >
              Pause
            </div>
            <template v-if="!routine_start">
              <div class="m-2">
                <div
                  class="text-right text-lg sm:text-5xl font-semibold leading-tight"
                  :class="{ 'text-red-600': seconds < 5 }"
                >
                  {{ seconds }} seconds
                </div>
                <div class="text-right text-sm uppercase text-gray-600">
                  {{ exercises_remaining }} exercises remaining
                </div>
              </div>
            </template>
          </div>
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
      if (this.routine_start) {
        return "N/A";
      } else {
        return this.routine.exercises[this.exercise_ix].name;
      }
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
      this.timer = setInterval(() => this.countdown(), 1000);
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
    countdown() {
      if (this.seconds > 0) {
        this.seconds--;
      } else {
        // If we have completed the routine, set the `completed` boolean, and
        // re-initialize variables
        if (this.exercise_ix + 1 == this.routine.exercises.length) {
          this.deactivate_timer();
          this.exercise_ix = 0;
          this.routine_start = true;
          this.completed = true;
          this.bg_color = "bg-white";
          return;
        }

        // If we have not yet completed the routine, the load the next
        // exercise, or rest period -- alternative between the two
        if (this.rest) {
          this.rest = false;
          this.exercise_ix += 1;
          this.seconds = this.routine.exercises[this.exercise_ix].duration;
        } else {
          this.rest = true;
          this.seconds = this.rest_duration;
        }
      }
    },
  },
};
</script>

