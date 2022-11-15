<template>
  <q-page class="flex flex-center">
    <div class="row" style="width: 80vw">
      <div class="col-2">
        <q-btn
          color="red"
          @click.prevent="backToIndexPage"
          label="Back to index page"
        />
      </div>
      <div class="col text-center"><div>Other Page</div></div>
    </div></q-page
  >
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'OtherPage',
  methods: {
    backToIndexPage() {
      this.$router.push({ name: 'IndexPage' });
    },
  },
});
</script>
