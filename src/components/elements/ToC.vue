<template>
    <ul :class="['level' , levelClass]">
        <li v-for="(item, index) in items" :key="`level-${item.level}-item-${index}`">
            <a :href="'#' + item.id">{{ item.text }}</a>
            <ToC v-if="item.children.length > 0" :items="item.children" :level="item.level"/>
        </li>
    </ul>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component({
  props: {
    items: Array,
    level: {
      type: Number,
      default: 0
    }
  }
})
export default class ToC extends Vue {
  get levelClass() {
    const className = `level-${(this as any).level}`;
    return className;
  }
}
</script>
<style lang="scss">
.level- {
  @for $level from 0 through 5 {
    &#{$level} {
        padding-left: #{$level * 10}px;
    }
  }
}
.level {
  list-style: none;
  text-align: left;
  margin: 0;

  a {
      margin-top: 1rem;
      display: block;
  }
}
</style>
