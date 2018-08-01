<template lang="html">
  <div class="color-line">
    <div class="color blue"></div>
    <div class="color yellow"></div>
    <div class="color red"></div>
    <div class="color green"></div>
    <div class="color blue"></div>
    <div class="color yellow"></div>
    <div class="color red"></div>
    <div class="color green"></div>
    <div class="color blue"></div>
    <div class="color yellow"></div>
    <div class="color red"></div>
    <div class="color green"></div>
    <div class="color blue"></div>
    <div class="color yellow"></div>
    <div class="color red"></div>
    <div class="color green"></div>
    <div class="color blue"></div>
    <div class="color yellow"></div>
    <div class="color red"></div>
    <div class="color green"></div>
  </div>
</template>

<script>
export default {
}
</script>

<style lang="css">
  .color-line {
    width: 100%;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
    margin: 0;
    position: fixed;
    top: 0;
    left: 0;
  }

  .color-line .color {
    -webkit-flex: 1;
    -ms-flex: 1;
    flex: 1;
    height: 3px;
  }

  .color-line .color.blue {
    background-color: #336699;
  }

  .color-line .color.yellow {
    background-color: #FFCC33;
  }

  .color-line .color.red {
    background-color: #CC3300;
  }

  .color-line .color.beige {
    background-color: #FFCC99;
  }

  .color-line .color.green {
    background-color: #339933;
  }
</style>
