<script lang="ts">
    import { defineComponent } from 'vue';

    import SuggestionItem from '@/components/SuggestionItem.vue';

    interface SearchData {
        wrapperType: string;
        value: string;
    }

    export default defineComponent({
        name: 'AutoSuggestions',
        components: {
            SuggestionItem,
        },
        props: {
            artists: {
                type: Array,
                default() {
                    return [];
                },
            },
            albums: {
                type: Array,
                default() {
                    return [];
                },
            },
            songs: {
                type: Array,
                default() {
                    return [];
                },
            },
        },
        computed: {
            canShowArtists(): boolean {
                return Boolean(this.artists.length > 0);
            },
            canShowAlbums(): boolean {
                return Boolean(this.albums.length > 0);
            },
            canShowSongs(): boolean {
                return Boolean(this.songs.length > 0);
            },
            canShowSomething(): boolean {
                return Boolean(this.canShowArtists || this.canShowAlbums || this.canShowSongs);
            },
        },
        methods: {
            doSearch(searchData: SearchData) {
                alert(`Type: ${searchData.wrapperType}, value: ${searchData.value}`);
            },
        },
    });
</script>

<template>
    <div v-show="canShowSomething" class="border rounded bg-slate-50 max-w-[600px] w-5/6 mx-auto mb-8 text-left p-1">
        <div v-show="canShowArtists">
            <h3>Artists:</h3>
            <SuggestionItem
                v-for="(item, index) in artists"
                :key="`artist_${index}`"
                :item="item"
                @useForSearch="doSearch"
            />
        </div>

        <div v-show="canShowAlbums">
            <h3>Albums:</h3>
            <SuggestionItem
                v-for="(item, index) in albums"
                :key="`album_${index}`"
                :item="item"
                @useForSearch="doSearch"
            />
        </div>

        <div v-show="canShowSongs">
            <h3>Songs:</h3>
            <SuggestionItem
                v-for="(item, index) in songs"
                :key="`song_${index}`"
                :item="item"
                @useForSearch="doSearch"
            />
        </div>
    </div>
</template>

<style scoped lang="scss"></style>
