<template>
  <div id="app">
    <p>Выбранный элемент: {{ selected }}</p>

<!--    <c-select v-model="selected" :select-options='a'>-->
<!--      <template v-slot:placeholder>Массив строк</template>-->
<!--    </c-select>-->
    <c-select v-model="selected" :select-options='b' @input="onInput($event)">
      <template v-slot:placeholder>Массив объектов</template>
    </c-select>
<!--    <c-select v-model="selected" :select-options='c' label-field="name" value-field="id">-->
<!--      <template v-slot:placeholder>Массив кастомных объектов</template>-->
<!--    </c-select>-->
<!--    <c-select v-model="selected" :select-options='d'>-->
<!--      <template v-slot:placeholder>Стандартный объект</template>-->
<!--    </c-select>-->
<!--    <c-select v-model="selected" :select-options='e'>-->
<!--      <template v-slot:placeholder>Кастомный объект</template>-->
<!--    </c-select>-->
<!--    <c-select v-model="selected" :select-options='e' value-field="id">-->
<!--      <template v-slot:placeholder>Кастомный объект</template>-->
<!--    </c-select>-->
  </div>
</template>

<script lang="ts">
  import {Component, Vue} from 'vue-property-decorator';
  import CSelect from "@/custom-select/CSelect.vue";

  @Component({
    components: {
      CSelect
    },

  })
  export default class App extends Vue {
    a = ['Label 1', 'Label 2', 'Label 3'];
    b = [{label: 'Label 1', value: 1}, {label: 'Label 2', value: 2}];
    c = [{name: 'Label 1', id: 1}, {name: 'Label 2', id: 2}];
    d = {value1: 'Label 1', value2: 'Label 2'};
    e = {value1: {title: 'Label 1', id: '1'}, value2: {title: 'Label 2', id: 2}};
    selected = 2;

    onInput(e) {
      console.log(e);
    }
  }
</script>

<style lang="scss">
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
</style>
