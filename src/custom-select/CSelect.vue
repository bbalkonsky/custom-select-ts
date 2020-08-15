<template>
  <div class="cselect" :class="{'is-active': isSelectActive}">
    <div class="cselect_header" @click="onSelectClick()">
      <span class="cselect_current" :class="{'is-placeholder': !currentSelectValue.length}">
        <template v-if="currentSelectValue.length">
          {{ currentSelectValue }}
        </template>
        <slot v-else>Please select</slot>
      </span>

      <div class="cselect_ico" @click.stop="onIcoClick()">
        {{ !currentSelectValue.length ? (isSelectActive ? '&uarr;' : '&darr;') : '&times;' }}
      </div>
    </div>

    <div class="cselect_body">
      <c-select-item v-for="value in values" :inputValue="'Value ' + value" :key="value"
                     @on-value-click="onValueClick($event)"/>
    </div>
  </div>
</template>

<script lang="ts">
import {Component, Prop, Vue} from "vue-property-decorator";
import CSelectItem from "@/custom-select/CSelectItem.vue";

@Component({
  components: {CSelectItem}
})

export default class CSelect extends Vue {
  @Prop() private values!: string[];

  isSelectActive = false;
  currentSelectValue = '';

  onSelectClick(): void {
    this.isSelectActive = !this.isSelectActive;
  }

  onValueClick(inputValue: string): void {
    this.currentSelectValue = inputValue;
    this.isSelectActive = false;
  }

  onIcoClick(): void {
    if (this.currentSelectValue.length) {
      this.currentSelectValue = '';
      this.isSelectActive = false;
    } else {
      this.isSelectActive = !this.isSelectActive;
    }
  }

}
</script>

<style scoped lang="scss">
$grey: #cccccc;

.cselect {
  position: relative;
  width: 300px;

  &_body {
    position: absolute;
    border: 1px solid $grey;
    border-top: 0;
    display: none;
    top: 100%;
    right: 0;
    left: 0;
  }

  &.is-active {
    .cselect_body {
      display: block;
    }
  }

  &_header {
    border: 1px solid $grey;
    cursor: pointer;
    display: flex;
  }

  &_current {
    font-size: 18px;
    line-height: 24px;
    padding: 8px;

    &.is-placeholder {
      color: $grey;
    }
  }

  &_ico {
    align-items: center;
    display: flex;
    flex-shrink: 0;
    justify-content: center;
    height: 40px;
    margin-left: auto;
    text-align: center;
    width: 40px;
  }
}
</style>
