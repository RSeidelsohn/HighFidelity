<script lang="ts">
    import { defineComponent } from 'vue';

    export default defineComponent({
        name: 'SuggestionItem',
        props: {
            item: {
                type: Object,
                required: true,
            },
        },
        computed: {
            relevantItemValue() {
                return this.item[`${this.item.wrapperType}Name`];
            },
        },
        methods: {
            selectForSearch() {
                this.$emit('useForSearch', { wrapperType: this.item.wrapperType, value: this.relevantItemValue });
            },
        },
    });
</script>

<template>
    <div
        class="mb-1 p-1 cursor-pointer hover:bg-violet-100 active:bg-violet-200 focus:outline-none focus:ring focus:ring-violet-300"
        @click="selectForSearch"
        title="Click for using this element for your search"
        tabindex="0"
    >
        {{ relevantItemValue }}
    </div>
</template>

<style scoped lang="scss"></style>
