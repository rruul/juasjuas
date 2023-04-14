<template>
  <TablaUsua />
</template>

<script>
import TablaUsua from '~/components/principal/tabla-users.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    TablaUsua
  },
  layout: 'principal'
}
</script>
