<template>
  <div class="cselect_item" @click="onOptionClick()">{{ optionLabel }}</div>
</template>

<script lang="ts">
import {Component, Emit, Prop, Vue} from "vue-property-decorator";
import Options, {numOrStr} from "@/custom-select/model/Options";

@Component

export default class CSelectItem extends Vue {
  @Prop() private optionLabel!: numOrStr;
  @Prop() private optionValue!: numOrStr;

  @Emit()
  onOptionClick(): Options {
    return {
      label: this.optionLabel,
      value: this.optionValue
    };
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
