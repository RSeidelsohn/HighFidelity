<script lang="ts">
    import { defineComponent } from 'vue';
    import axios from 'axios';

    import AutoSuggestions from '@/components/AutoSuggestions.vue';
    import SearchResults from '@/components/SearchResults.vue';

    const ITUNES_API = 'https://itunes.apple.com/search';
    const AUTOSUGGEST_DELAY = 200; // milliseconds delay before the next axios request for autosuggestions

    let controllerSongs: AbortController;
    let controllerAlbums: AbortController;
    let controllerArtists: AbortController;

    export default defineComponent({
        name: 'SearchForm',
        components: {
            AutoSuggestions,
            SearchResults,
        },
        data() {
            return {
                searchText: '',
                suggestedArtists: [],
                suggestedAlbums: [],
                suggestedSongs: [],
                searchResults: [],
                isBusy: false,
                timeoutId: null as number | null,
            };
        },
        methods: {
            clearSearchResultLists() {
                this.suggestedArtists = [];
                this.suggestedAlbums = [];
                this.suggestedSongs = [];
                this.searchResults = [];
                this.isBusy = false;
            },
            submitForm() {
                this.isBusy = true;

                if (this.searchText === '') {
                    this.clearSearchResultLists();
                    return;
                }
            },
            planAutoSuggestUpdate() {
                if (this.timeoutId) {
                    window.clearTimeout(this.timeoutId);
                }

                this.timeoutId = setTimeout(() => {
                    this.updateAutoSuggest();
                }, AUTOSUGGEST_DELAY);
            },
            updateAutoSuggest() {
                this.isBusy = true;

                if (controllerSongs) {
                    controllerSongs.abort();
                }

                if (controllerArtists) {
                    controllerArtists.abort();
                }

                if (controllerAlbums) {
                    controllerAlbums.abort();
                }

                if (this.searchText === '') {
                    this.clearSearchResultLists();
                    return;
                }

                controllerSongs = new AbortController();
                controllerArtists = new AbortController();
                controllerAlbums = new AbortController();

                let promiseSuggestedSongs = axios.get(
                    `${ITUNES_API}?media=music&entity=song&limit=10&term=${encodeURIComponent(this.searchText)}`,
                    { signal: controllerSongs.signal }
                );
                let promiseSuggestedArtists = axios.get(
                    `${ITUNES_API}?media=music&entity=musicArtist&limit=10&term=${encodeURIComponent(this.searchText)}`,
                    { signal: controllerArtists.signal }
                );
                let promiseSuggestedAlbums = axios.get(
                    `${ITUNES_API}?media=music&entity=album&limit=10&term=${encodeURIComponent(this.searchText)}`,
                    { signal: controllerAlbums.signal }
                );

                axios
                    .all([promiseSuggestedSongs, promiseSuggestedArtists, promiseSuggestedAlbums])
                    .then(
                        axios.spread((...responses) => {
                            this.suggestedSongs = responses[0].data.results;
                            this.suggestedArtists = responses[1].data.results;
                            this.suggestedAlbums = responses[2].data.results;
                        })
                    )
                    .catch((errors) => {
                        console.error(errors);
                    })
                    .finally(() => {
                        this.isBusy = false;
                    });
            },
        },
    });
</script>

<template>
    <form @submit.prevent="submitForm" :class="[isBusy ? 'is-busy' : '']">
        <label>Start typing the name of either an artist, album or song you are searching for:</label><br />
        <input
            type="text"
            name="searchText"
            v-model="searchText"
            @input="planAutoSuggestUpdate"
            class="border-2 rounded p-[0.5em] my-8"
            tabindex="0"
        />
        <AutoSuggestions :artists="suggestedArtists" :albums="suggestedAlbums" :songs="suggestedSongs" />
        <SearchResults :results="searchResults" />
    </form>
</template>

<style scoped lang="scss">
    .is-busy::before {
        background-color: #bbb;
        bottom: 0;
        content: '';
        cursor: wait;
        left: 0;
        opacity: 0.5;
        position: absolute;
        right: 0;
        top: 0;
    }
</style>
