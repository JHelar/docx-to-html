<template>
    <section class="page">
        <component v-for="(element, eIndx) in elements" v-bind:is="element.component" :data="element.data" :key="'element-' + eIndx"></component> 
    </section>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

import Header from "./Header.vue";
import Paragraph from "./Paragraph.vue";
import Picture from "./Picture.vue";
import List from "./List.vue";
import Hyperlink from "./Hyperlink.vue";

@Component({
  props: {
    elements: Array
  },
  components: {
    Header,
    Paragraph,
    Picture,
    List,
    Hyperlink
  }
})
export default class Page extends Vue {}
</script>
<style lang="scss">
.page {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
  padding: 1rem;
  box-shadow: 0 0 2px 3px #2c3e50;
  text-align: left;
}
</style>