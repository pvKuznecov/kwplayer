<template>
    <!-- <div id="app"> -->
        <!-- <MusicPlaylist/> -->
        <MainView/>
    <!-- </div> -->
</template>
<style src="./main.css"></style>
<script>
    // import MusicPlaylist from './components/MusicPlaylist/MusicPlaylist.vue';
    import MainView from './view/MainView/MainView.vue';

    export default {
        name: 'App',
        components: {
            MainView
        },
    };
</script>