<template>
    <a :href="href">{{ text }}</a>
</template>
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

@Component({
    props: {
        data: Object
    }
})
export default class Hyperlink extends Vue {
    href: string = (this as any).data.href;
    text: string = (this as any).data.text;
}
</script>

