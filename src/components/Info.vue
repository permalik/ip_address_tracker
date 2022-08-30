<template>
  <section>
    <div>
      <h2>IP Address</h2>
      <p>{{info.address}}</p>
    </div>
    <div>
      <h2>Region</h2>
      <p>{{info.city}}, {{info.state}}</p>
    </div>
    <div>
      <h2>Timezone</h2>
      <p>{{info.timezone}}</p>
    </div>
    <div>
      <h2>ISP</h2>
      <p>{{info.isp}}</p>
    </div>
  </section>
</template>

<script>
export default {
  props: ['info']
};
</script>

<style scoped>

</style>