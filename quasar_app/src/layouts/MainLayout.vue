<template>
  <q-layout view="lHh Lpr fff" class="bg-grey-1">
    <router-view />
    <q-footer bordered class="bg-white text-black text-center q-pa-sm">
      <span class="">© 2020-2021 Saurav Jaiswal.</span>
    </q-footer>
  </q-layout>

</template>

<script>

  export default {
    data() {
      return {
        leftDrawerOpen: false,
        links1: [
          {icon: 'list_alt', text: 'TODO', path: '/'},
        ],
      }
    },
    computed: {

    },
     methods: {

    }
}
</script>

