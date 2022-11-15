<template>
  <div class="q-pa-md">
    <q-layout view="hHh lpr fFf">
      <q-header reveal bordered class="bg-white text-black">
        <q-toolbar>
          <q-toolbar-title
            ><div class="text-center">Simple Todo Tracker</div></q-toolbar-title
          >
        </q-toolbar>
      </q-header>

      <q-page-container>
        <router-view />
      </q-page-container>

      <q-footer reveal bordered class="bg-white text-black">
        <q-toolbar>
          <q-toolbar-title><div class="text-center"></div></q-toolbar-title>
        </q-toolbar>
      </q-footer>
    </q-layout>
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'MainLayout',
});
</script>
