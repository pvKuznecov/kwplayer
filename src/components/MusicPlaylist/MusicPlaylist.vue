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
                FindTracks: [],
                EditPlaylistMode: false,
                VolumeLvl: 0.5,
                Play: false,
                TrackTime: { current: 0, duration: 0},
                RepeatMode: "",
            };
        },
        methods: {
            onFilesSelected(files) {
                this.FindTracks = files.filter(file => file.type === 'audio/mpeg');
                console.log("FindTracks", this.FindTracks);
            },
            onSelectedTrack(trackData) {
                if (this.TargetTrack.id !== trackData.id) {
                    this.Play = false;
                    this.TargetTrack = trackData;

                    setTimeout(() => {
                        let AudioElement = document.querySelector('#main_audio');
                        let TrackTimeElement = document.querySelector('#main_tracktime');

                        if (AudioElement) {
                            let CSec = Math.trunc(AudioElement.duration);
                            TrackTimeElement.max = CSec;
                            this.TrackTime = { current: 0, duration: Math.trunc(CSec / 60) + ":" + CSec % 60};
                        }
                    }, 100);
                }
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
            Chng_RepeatMode() {
                let AudioElement = document.querySelector('#main_audio');

                switch (this.RepeatMode){
                    case "":
                        this.RepeatMode = "one";
                        
                        AudioElement.loop = true;
                        break;
                    case "one":
                        this.RepeatMode = "all";
                        AudioElement.loop = false;
                        break;
                    default:
                        this.RepeatMode = "";
                        AudioElement.loop = false;
                        break;
                }
            },
            Chng_TrackRangeTime() {
                let AudioElement = document.querySelector('#main_audio');
                let TrackTimeElement = document.querySelector('#main_tracktime');

                // this.TrackTime.current = (TrackTimeElement) ? TrackTimeElement.value : 0;
                AudioElement.currentTime = TrackTimeElement.value;
            },
            ShowerTime(iSec) {
                if (iSec && typeof iSec == 'number') {
                    let inpSec = Math.trunc(iSec);
                    let strMin = (Math.trunc(inpSec / 60) < 10) ? ("0" + Math.trunc(inpSec / 60)) : Math.trunc(inpSec / 60);
                    let strSec = (inpSec % 60 < 10) ? ("0" + (inpSec % 60)) : (inpSec % 60);

                    return strMin + ":" + strSec;
                } else {
                    return "00:00";
                }
            },
            onTimeUpdate(event) {
                const audio = event.target;
                // Получаем текущую позицию воспроизведения и общую продолжительность
                this.TrackTime.current = audio.currentTime;
                this.TrackTime.duration = audio.duration || 0;

                if (this.TrackTime.current == this.TrackTime.duration) {
                    this.PlayerAction_Pause();
                }
      
                let TrackTimeElement = document.querySelector('#main_tracktime');
                TrackTimeElement.value = this.TrackTime.current;
            }
            
        }
    }
</script>