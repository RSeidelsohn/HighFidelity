<script lang="ts">
    import { defineComponent } from 'vue';
    import SearchForm from '@/components/SearchForm.vue'; // @ is an alias to /src

    export default defineComponent({
        name: 'HomeView',
        components: {
            SearchForm,
        },
    });
</script>

<template>
    <div class="home">
        <h1 class="text-3xl">HighFidelity</h1>
        <h2 class="text-xl mb-4">Your music search engine</h2>

        <SearchForm />
    </div>
</template>
