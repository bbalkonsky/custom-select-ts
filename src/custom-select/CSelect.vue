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
      <c-select-item v-for="(value, idx) in optionList" :option-title="value.label" :option-value="value.value" :key="idx"
                     @on-value-click="onValueClick($event)"/>
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
  @Prop({default: 'label'}) private labelField!: string;
  @Prop() private valueField!: string;

  isSelectActive = true;
  currentSelectValue = '';


  onSelectClick(): void {
    this.isSelectActive = !this.isSelectActive;
  }

  onValueClick(inputValue: string): void {
    this.currentSelectValue = inputValue.toString();
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

  // альтернативой было делать наоборот - разные компоненты в шаблоне для разных типов входных параметров
  // но такой вариант мне просто ближе (несмотря на теоритический проигрыш по скорости)
  get optionList(): any[] {
    if (Array.isArray(this.selectOptions)) {
      return this.arrayHandler(this.selectOptions);
    } else if (typeof this.selectOptions === 'object') {
      return this.objectHandler(this.selectOptions);
    } else {
      return [];
    }
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
