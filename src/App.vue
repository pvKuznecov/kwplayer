<template>
    <div id="app">
        <MusicPlaylist/>
    </div>
</template>
<style src="./main.css"></style>
<script>
    import MusicPlaylist from './components/MusicPlaylist/MusicPlaylist.vue';

    export default {
        name: 'App',
        components: {
            MusicPlaylist
        },
    };
</script>