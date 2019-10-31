<template>
  <div class="icon-top-button">
    <i :class="icon"></i>
    <span>{{ name }}</span>
  </div>
</template>
<script>
export default {
  props: {
    name: {
      type: String
    },
    icon: {
      type: String,
      default: 'el-icon-close'
    }
  }
};
</script>
<style lang="scss" scoped>
$font-family: 'element-icons', 'Tahoma', 'Microsoft YaHei';
$button-text-size: 12px;
$button-icon-size: 24px;
$active-color: #ff4526;
.icon-top-button {
  background-color: #fff;
  border: 1px rgb(220, 223, 230) solid;
  border-radius: 10px;
  display: block;
  padding: 6px 15px;
  margin: 0 8px 8px 8px;
  outline: none;
  text-align: center;
  cursor: pointer;
  font: $button-icon-size $font-family !important;
  &:hover {
      color: $active-color;
  }
  & span {
    font-size: $button-text-size;
    display: block;
  }
}
</style>
