<template>
  <paperwork-list :categoryId="categoryId"></paperwork-list>
</template>

<script setup lang="ts">
import { useRoute } from 'vue-router';
import { onMounted, computed, watch } from 'vue';
import { useQuasar } from 'quasar';
import { usePaperworkStore } from 'src/stores/paperworkStore';
import type { GenericResponseData } from 'src/Models/GenericResponseData';
import PaperworkList from '../Paperwork/PaperworkList.vue';

const $route = useRoute();
const $q = useQuasar();
const categoryId = computed(() => $route.params.id as string);
const paperworkStore = usePaperworkStore();
// watch works directly on a ref
watch(categoryId, async (newCategoryId) => {
  $q.loading.show({
    message: 'Loading ...',
  });
  try {
    await paperworkStore.getPaperworksByCategoryId(newCategoryId);
    $q.loading.hide();
  } catch (err: unknown) {
    $q.loading.hide();
    const errorMessage = (err as GenericResponseData).message || String(err);
    $q.notify({
      type: 'negative',
      message: errorMessage,
    });
  }
});

onMounted(() => {
  $q.loading.show({
    message: 'Loading ...',
  });
  paperworkStore
    .getPaperworksByCategoryId($route.params.id as string)
    .then(() => {
      $q.loading.hide();
    })
    .catch((err: unknown) => {
      $q.loading.hide();
      const errorMessage = (err as GenericResponseData).message || String(err);
      $q.notify({
        type: 'negative',
        message: errorMessage,
      });
    });
});
</script>

<style lang="sass" scoped></style>
