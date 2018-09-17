<template>
    <p>{{ text }}</p>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component({
    props: {
        data: Object
    }
})
export default class Paragraph extends Vue {
    text: string = (this as any).data.text
}
</script>

