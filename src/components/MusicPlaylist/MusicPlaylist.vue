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
                VolumeLvl: 0.5,
                Play: false,
                TrackTime: { current: 0, all: 0}
            };
        },
        computed: {
            TTrackData() {
                const AudioElement = document.querySelector('#main_audio');
                console.log("AudioElement", AudioElement.duration);
                return true;
            },
        },
        methods: {
            onFilesSelected(files) {
                this.selectedTracks = files.filter(file => file.type === 'audio/mpeg');
            },
            onSelectedTrack(trackData) {
                if (this.TargetTrack.id !== trackData.id) {
                    this.Play = false;
                }
                
                this.TargetTrack = trackData;                
            },
            PlayerAction_Play() {
                let AudioElement = document.querySelector('#main_audio');
                AudioElement.play();
                this.Play = true;
            },
            PlayerAction_Pause() {
                let AudioElement = document.querySelector('#main_audio');
                AudioElement.pause();
                this.Play = false;
            },
            PlayerAction_VolPlus() {
                let AudioElement = document.querySelector('#main_audio');
                if (this.VolumeLvl < 1) {
                    // this.VolumeLvl += 0.1;
                    this.VolumeLvl = parseFloat((this.VolumeLvl + 0.1).toFixed(1));
                    console.log("VolumeLvl", this.VolumeLvl);
                }
                AudioElement.volume = this.VolumeLvl;
            },
            PlayerAction_VolMinus() {
                let AudioElement = document.querySelector('#main_audio');
                if (this.VolumeLvl > 0) {
                    this.VolumeLvl = parseFloat((this.VolumeLvl - 0.1).toFixed(1));
                    console.log("VolumeLvl", this.VolumeLvl);
                }
                AudioElement.volume = this.VolumeLvl;
            },
        }
    }
</script>