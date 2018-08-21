<template>
    <component v-bind:is="level" :id="id">{{ text }}</component>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component({
    props: {
        data: Object
    }
})
export default class Header extends Vue {
    level: string = `h${(this as any).data.level}`;
    text: string = (this as any).data.text;
    id: string = (this as any).data.id;
}
</script>
