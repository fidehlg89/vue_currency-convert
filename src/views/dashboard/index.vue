<template>
  <div id="dashboard-page">
    <CurrencyConvert />
  </div>
</template>

<script>
import CurrencyConvert from "../currency";
export default {
  name: "Dashboard",
  components: {
    CurrencyConvert,
  },
};
</script>

<style scoped></style>
