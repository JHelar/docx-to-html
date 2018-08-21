<template>
    <ul>
        <li v-for="(item, index) in items" :key="'item-' + index">{{ item.text }}</li>
    </ul>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component({
    props: {
        data: Object
    }
})
export default class List extends Vue {
    items: any[] = (this as any).data.items
}
</script>