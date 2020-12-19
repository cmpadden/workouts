<template>
  <div>
    <NavBar />
    <div>
      <div class="flex flex-wrap">
        <div
          v-for="w in routines"
          :key="w.title"
          class="max-w-sm md:max-w-lg mx-auto bg-white overflow-hidden mb-4 shadow-lg"
        >
          <div class="md:flex">
            <div class="">
              <div class="p-4 bg-gray-200 border-b-2 border-gray-500">
                <div class="uppercase tracking-wide text-sm font-semibold">
                  {{ w.name }}
                </div>
                <div
                  class="my-1 text-md leading-tight font-medium text-gray-600"
                >
                  by {{ w.author }}
                </div>
              </div>
              <div class="text-gray-500 p-4">
                {{ w.description }}
              </div>
              <NuxtLink
                class="mx-4 mb-4 px-4 py-1 text-sm text-orange-600 font-semibold border border-orange-600 hover:bg-orange-200 focus:outline-none focus:ring-2 focus:ring-green-600 focus:ring-offset-2"
                type="button"
                :to="`/routines/${w.slug}`"
              >
                Let's Go!</NuxtLink>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import NavBar from "@/components/navbar.vue";
export default {
  components: {
    NavBar,
  },
  async asyncData({ $content }) {
    // fetch routines, without the exercises list
    const routines = await $content("routines").without(["exercises"]).fetch();
    return { routines };
  },
};
</script>

