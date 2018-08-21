<template>
    <img :src="src" alt="An Image">
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component({
    props: {
        data: Object
    }
})
export default class Picture extends Vue {
    src: string = (this as any).data.src
}
</script>
