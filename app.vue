<template>
  <div>
      <div class="m-auto p-6 bg-gray-50 h-24 fixed">
          <SideBar />
      </div>
      <div class="pl-32 pt-12">
          <NuxtPage keep-alive />
      </div>
  </div>
</template>

<script>
import SideBar from '~/components/sidebar/SideBar.vue'

export default {
    components: {
        SideBar
    }
}
</script>