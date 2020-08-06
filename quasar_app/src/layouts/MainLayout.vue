<template>
  <q-layout view="lHh Lpr fff" class="bg-grey-1">
    <q-page-container>
      <router-view />
    </q-page-container>
    <q-footer bordered class="bg-white text-black text-center q-pa-sm">
      <span class="">© 2020-2021 Saurav Jaiswal.</span>
    </q-footer>
  </q-layout>

</template>

<script>

  export default {
    data() {
      return {
        links1: [
          {icon: 'list_alt', text: 'TODO', path: '/'},
        ],
      }
    }
}
</script>

