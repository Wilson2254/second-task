<template>
  <div class="main">
    <div class="fields">
      <div class="one_field"></div>
      <div class="two_field"></div>
      <div class="three_fields"></div>
      <div class="four_fields"></div>
    </div>
    <div class="controls"></div>
  </div>
</template>

<script>
export default {};
</script>

<style scoped>
.main {
  display: flex;
  justify-content: center;
}

.fields {
  display: flex;
  flex-wrap: wrap;
  width: 310px;
}

.one_field,
.two_field,
.three_fields,
.four_fields {
  flex: 1 1 calc(33.33%); /* отнимем margin и скажем растягиваться */
  margin: 1px;
  box-sizing: border-box; /* чтобы внутренний отступ не влиял когда там будет текст... */
  width: 150px;
  height: 150px;
  border: 1px solid;
}

.one_field {
  border-top-left-radius: 100%;
  background-color: #1e90ff;
  opacity: 0.6;
}

.two_field {
  border-top-right-radius: 100%;
  background-color: #ff5643;
  opacity: 0.6;
}

.three_fields {
  border-bottom-left-radius: 100%;
  background-color: #feef33;
  opacity: 0.6;
}

.four_fields {
  border-bottom-right-radius: 100%;
  background-color: #bede15;
  opacity: 0.6;
}
</style>