<template>
  <div tabindex="0" class="cselect" :class="{'is-active': isSelectActive}" @keyup.up="onUp()" @keyup.down="onDown()">
    <div class="cselect_header" @click="onSelectClick()">
      <span class="cselect_current" :class="{'is-placeholder': currentSelectIdx === -1}">
        <template v-if="currentSelectIdx >= 0">
          {{ optionList[currentSelectIdx].label }}
        </template>
        <slot v-else>Please select</slot>
      </span>

      <div class="cselect_ico" @click.stop="onIcoClick()">
        {{ currentSelectIdx === -1 ? (isSelectActive ? '&uarr;' : '&darr;') : '&times;' }}
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
  @Prop() private value!: numOrStr;
  @Prop({default: 'label'}) private labelField!: string;
  @Prop() private valueField!: string;

  isSelectActive = false;
  // currentSelect: Options;

  currentSelectIdx = -1;

  onSelectClick(): void {
    this.isSelectActive = !this.isSelectActive;
  }

  onOptionClick(selected: Options): void {
    // this.currentSelect = selected;
    this.currentSelectIdx = this.optionList.findIndex(item => item.label === selected.label && item.value === selected.value);
    this.isSelectActive = false;
    this.inputChangeEmit();
  }

  onIcoClick(): void {
    if (this.currentSelectIdx >= 0) {
      this.currentSelectIdx = -1;
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
      const foundValue = resultArray.findIndex(item => item.value === this.value);
      foundValue != -1 ? this.currentSelectIdx = foundValue : null;
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

  // т.к. input от change в случае селекта отличется только моментом срабатывания, просто эмитим их в такой последовательности
  inputChangeEmit(): void {
    const activeValue = this.optionList[this.currentSelectIdx].value;
    this.$emit('input', activeValue);
    this.$emit('change', activeValue);
  }

  onUp(): void {
    if (this.currentSelectIdx > 0) {
      this.currentSelectIdx = this.currentSelectIdx - 1;
      this.inputChangeEmit();
    }
  }

  onDown(): void {
    if (this.optionList.length - 1 > this.currentSelectIdx) {
      this.currentSelectIdx = this.currentSelectIdx + 1;
      this.inputChangeEmit();
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
