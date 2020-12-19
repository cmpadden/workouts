<template>
  <div>
    <nav
      class="relative flex flex-wrap items-center justify-between navbar-expand-lg bg-gradient-to-r from-red-700 via-purple-500 to-blue-700 border-b-4 border-orange-500 mb-3"
    >
      <div
        class="container px-4 mx-auto flex flex-wrap items-center justify-between"
      >
        <div
          class="w-full relative flex justify-between sm:w-auto px-4 sm:static sm:block sm:justify-start"
        >
          <n-link
            class="text-sm font-black leading-relaxed inline-block mr-4 py-2 whitespace-no-wrap uppercase text-white"
            to="/"
          >
            {{ title }}
          </n-link>
          <button
            class="text-white cursor-pointer text-xl leading-none px-3 py-1 border border-solid border-transparent rounded bg-transparent block sm:hidden outline-none focus:outline-none"
            type="button"
            @click="toggleNavbar"
          >
            <svg
              class="h-6 w-6"
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M4 6h16M4 12h16M4 18h16"
              />
            </svg>
          </button>
        </div>
        <div
          :class="{ hidden: !showMenu, flex: showMenu }"
          class="sm:flex sm:flex-grow items-center"
        >
          <ul class="flex flex-col sm:flex-row list-none ml-auto">
            <li class="nav-item">
              <n-link
                to="/about"
                class="px-3 py-2 flex items-center text-xs uppercase font-bold leading-snug text-white hover:opacity-75"
              >
                <span class="ml-2">About</span>
              </n-link>
            </li>
          </ul>
        </div>
      </div>
    </nav>
  </div>
</template>

<script>
export default {
  name: "navbar",
  data() {
    return {
      showMenu: false,
      title: "Workouts",
    };
  },
  methods: {
    toggleNavbar: function () {
      this.showMenu = !this.showMenu;
    },
  },
};
</script>
