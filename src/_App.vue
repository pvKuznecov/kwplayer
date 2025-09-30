<template>
    <div id="app">
        <h1>KWPLAYER</h1>
        <MusicUploader @files-selected="onFilesSelected" />
        <MusicPlayer :tracks="selectedTracks" />
    </div>
</template>
<style src="./main.css"></style>
<script>
    import MusicPlayer from './components/MusicPlayer/MusicPlayer.vue';
    import MusicUploader from './components/MusicUploader/MusicUploader.vue';

    export default {
        name: 'App',
        components: {
            MusicUploader,
            MusicPlayer
        },
        data() {
            return {
                selectedTracks: []
            };
        },
        methods: {
            onFilesSelected(files) {
                console.log("files", files);
                console.log("aaa", files.filter(file => file.type === 'audio/mpeg'));
                this.selectedTracks = files.filter(file => file.type === 'audio/mpeg');
                console.log("selectedTracks", this.selectedTracks);
            }
        }
    };
</script>