<template>
  <section>
    <div class="container">
      <div>
        <h2>IP Address</h2>
        <p>{{ info.address }}</p>
      </div>
      <div>
        <h2>Location</h2>
        <p>{{ info.city }}, {{ info.state }}</p>
      </div>
      <div>
        <h2>Timezone</h2>
        <p>{{ info.timezone }}</p>
      </div>
      <div>
        <h2>ISP</h2>
        <p>{{ info.isp }}</p>
      </div>
    </div>
  </section>
</template>

<style scoped>
section {
  align-items: center;
  background: white;
  border-radius: 15px;
  color: black;
  display: flex;
  flex-flow: row nowrap;
  justify-content: center;
  position: absolute;
  top: calc(20vh + 50px);
  z-index: 999;
}
.container {
  align-items: center;
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
  padding: 2em;
  position: relative;
  text-align: center;
}
section div {
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
}
section div:not(:last-child) {
  margin-block-end: 20px;
}
h2 {
  color: var(--grey-500);
  font-size: .85em;
  margin-block-end: 10px;
  text-transform: uppercase;
}
p {
  font-weight: var(--fontweight-medium);
  font-size: 1.25em;
}
@media (min-width: 850px) {
  .container {
    align-items: flex-start;
    display: flex;
    flex-flow: row nowrap;
    justify-content: center;
  }
  section div:not(:first-child) {
    padding-left: 15px;
  }
  section div:not(:last-child) {
    padding-right: 30px;
  }
  section div:not(:last-child)::before {
    background: black;
    content: '';
    height: 55px;
    position: absolute;
    right: 0;
    top: 0;
    width: 2px;
  }
}
</style>

<script>
export default {
  props: ["info"]
};
</script>
