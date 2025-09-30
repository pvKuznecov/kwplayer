<template src="./MusicPlaylist.html"></template>
<style src="./MusicPlaylist.css"></style>
<script>
    import MusicUploader from '../MusicUploader/MusicUploader.vue';
    import MusicPlayer from '../MusicPlayer/MusicPlayer.vue';

    export default {
        name: "MusicPlaylist",
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
    }
</script>