<template>
  <div class="cselect_item" @click="onValueClick(optionValue)">{{ optionTitle }}</div>
</template>

<script lang="ts">
import {Component, Emit, Prop, Vue} from "vue-property-decorator";

@Component

export default class CSelectItem extends Vue {
  @Prop() private optionTitle!: string | number;
  @Prop() private optionValue!: string | number;

  @Emit()
  onValueClick(val: string) {
    return val;
  }
}
</script>

<style scoped lang="scss">
.cselect_item {
  cursor: pointer;
  font-size: 16px;
  line-height: 24px;
  padding: 8px;

  &:hover {
    background-color: #f2f2f2;
  }
}
</style>
