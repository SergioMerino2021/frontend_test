<template>
  <div class="bg-gray-800 py-3 shadow-lg ">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex items-center justify-center md:justify-between h-auto">
        <div class="flex-shrink-0 hidden md:block">
          <a href="/" class="text-xl font-bold">Laravel + Vue</a>
        </div>
        <div class="">
          <div class="ml-10 flex items-baseline space-x-4">
            <router-link to="/" class="text-gray-300 hover:text-green-400 px-3 py-2 rounded-md text-sm font-medium">Client</router-link>
            <router-link to="/product" class="text-gray-300 hover:text-green-400 px-3 py-2 rounded-md text-sm font-medium">Producte</router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
  <router-view/>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
