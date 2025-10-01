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
                Playlist: { name: "New playlist" },
                selectedTracks: [],
                EditPlaylistMode: false,
            };
        },
        methods: {
            onFilesSelected(files) {
                this.selectedTracks = files.filter(file => file.type === 'audio/mpeg');
            }
        }
    }
</script>