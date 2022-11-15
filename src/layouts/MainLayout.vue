<template>
  <q-layout view="hHh lpR fFf">
    <q-header reveal bordered class="bg-white text-black">
      <q-toolbar>
        <q-toolbar-title
          ><div class="text-center">Simple Notes</div></q-toolbar-title
        >
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>

    <q-footer reveal bordered class="bg-white text-white">
      <q-toolbar>
        <q-toolbar-title> </q-toolbar-title>
      </q-toolbar>
    </q-footer>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'MainLayout',
});
</script>
