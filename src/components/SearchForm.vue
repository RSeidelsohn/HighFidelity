<script lang="ts">
    import { defineComponent } from 'vue';
    import axios from 'axios';

    import AutoSuggesions from '@/components/AutoSuggestions.vue';
    import SearchResults from '@/components/SearchResults.vue';

    const ITUNES_API = 'https://itunes.apple.com/search';

    export default defineComponent({
        name: 'SearchForm',
        data() {
            return {
                searchText: '',
                suggestedArtists: [],
                suggestedAlbums: [],
                suggestedSongs: [],
                searchResults: [],
                isBusy: false,
            };
        },
        props: {},
        methods: {
            clearSearchResultLists() {
                this.suggestedArtists = [];
                this.suggestedAlbums = [];
                this.suggestedSongs = [];
                this.searchResults = [];
            },
            submitForm() {
                this.isBusy = true;

                if (this.searchText === '') {
                    this.clearSearchResultLists();
                    return;
                }
            },
            updateAutoSuggest() {
                this.isBusy = true;

                if (this.searchText === '') {
                    this.clearSearchResultLists();
                    return;
                }
            },
        },
    });
</script>

<template>
    <form @submit.prevent="submitForm">
        <label> Start typing the name of either an artist, ablum or song you are searching for: </label>
        <input type="text" name="searchText" v-model="searchText" @input="updateAutoSuggest" />
        <SearchResults />
        <SearchResults />
    </form>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
    h3 {
        margin: 40px 0 0;
    }
    ul {
        list-style-type: none;
        padding: 0;
    }
    li {
        display: inline-block;
        margin: 0 10px;
    }
    a {
        color: #42b983;
    }
</style>
