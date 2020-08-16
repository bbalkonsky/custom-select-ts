<template>
  <div tabindex="0" class="cselect" :class="{'is-active': isSelectActive}">
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
      <c-select-item v-for="(value, idx) in optionList" :option-label="value.label" :option-value="value.value"
                     :key="idx"
                     @on-option-click="onOptionClick($event)"/>
    </div>
  </div>
</template>

<script lang="ts">
import {Component, Prop, Vue} from "vue-property-decorator";
import CSelectItem from "@/custom-select/CSelectItem.vue";
import Options, {numOrStr} from "@/custom-select/model/Options";

@Component({
  components: {CSelectItem}
})

export default class CSelect extends Vue {
  @Prop() private selectOptions: numOrStr[] | object | undefined;
  @Prop() private value!: string;
  @Prop({default: 'label'}) private labelField!: string;
  @Prop() private valueField!: string;

  isSelectActive = false;
  currentSelectValue = '';

  onSelectClick(): void {
    this.isSelectActive = !this.isSelectActive;
  }

  onOptionClick(selected: Options): void {
    this.currentSelectValue = selected.label.toString();
    this.isSelectActive = false;

    // т.к. input от change в случае селекта отличется только моментом срабатывания, просто эмитим их в такой последовательности
    this.$emit('input', selected.value);
    this.$emit('change', selected.value);
  }

  onIcoClick(): void {
    if (this.currentSelectValue.length) {
      this.currentSelectValue = '';
      this.isSelectActive = false;
    } else {
      this.isSelectActive = !this.isSelectActive;
    }
  }

  // альтернативой было делать наоборот - разные компоненты в шаблоне для разных типов входных параметров
  // но я это осознал, когда уже сделал вот так ¯\_(ツ)_/¯
  get optionList(): Options[] {
    let resultArray: Options[] = [];
    if (Array.isArray(this.selectOptions)) {
      resultArray = this.arrayHandler(this.selectOptions);
    } else if (typeof this.selectOptions === 'object') {
      resultArray = this.objectHandler(this.selectOptions);
    }

    // для работы v-model
    if (this.value) {
      for (const option of resultArray) {
        if (option.value === this.value) {
          this.currentSelectValue = option.label.toString();
          break;
        }
      }
    }

    return resultArray;
  }

  arrayHandler(arr: any[]): Options[] {
    if (typeof arr[0] === 'number' || typeof arr[0] === 'string') {
      return arr.map(item => {
        return this.getOptionsObject(item);
      })
    } else {
      const valueField = this.valueField ? this.valueField : 'value';
      return arr.map(item => {
        return this.getOptionsObject(item[this.labelField], item[valueField]);
      })
    }
  }

  objectHandler(obj: any): Options[] {
    const objKeys = Object.keys(obj);
    if (typeof obj[objKeys[0]] === 'number' || typeof obj[objKeys[0]] === 'string') {
      return objKeys.map(key => {
        return this.getOptionsObject(obj[key], key);
      })
    } else {
      const labelField = this.valueField ? this.valueField : 'title';
      return objKeys.map(key => {
        return this.getOptionsObject(obj[key][labelField], key);
      })
    }
  }

  getOptionsObject(label: numOrStr, value: numOrStr = label): Options {
    return {
      label,
      value
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
