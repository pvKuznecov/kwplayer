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
                TargetTrack: false,
                selectedTracks: [],
                EditPlaylistMode: false,
                Volume: "0.80"
            };
        },
        computed: {
            TTrackData() {
                const AudioElement = document.querySelector('#main_audio');
                console.log("AudioElement", AudioElement.duration);
                return true;
            }
        },
        methods: {
            onFilesSelected(files) {
                this.selectedTracks = files.filter(file => file.type === 'audio/mpeg');
            },
            onSelectedTrack(trackData) {
                // const AudioElement = document.querySelector('#main_audio');
                this.TargetTrack = trackData;
            },
            PlayerAction_Play() {
                let AudioElement = document.querySelector('#main_audio');
                AudioElement.play();
            },
            PlayerAction_Pause() {
                let AudioElement = document.querySelector('#main_audio');
                AudioElement.pause();
            },
        }
    }
</script>