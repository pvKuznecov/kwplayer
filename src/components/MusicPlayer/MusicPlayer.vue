<template src="./MusicPlayer.html"></template>
<style src="./MusicPlayer.css"></style>
<script>
    import MusicUploader from '../MusicUploader/MusicUploader.vue';

    export default {
        name: "MusicPlayer",
        components: {
            MusicUploader,
        },
        data() {
            return {
                currentIndex: 0,
                TargetTrack: false,
                FindTracks: [],
                VolumeLvl: 0.5,
                VolumeLvl_save: 0.5,
                Play: false,
                Shuffle: false,
                SilentMode: false,
                TrackTime: { current: 0, duration: 0},
                RepeatMode: "all",
                tableShow: 0,
                audioDurations: {}, // Объект для "длительностей"
                tableShowConfig: {
                    id: true,
                    artist: true,
                    album: true,
                    title: true,
                    duration: true,
                    genre: true
                },
                showTagsData: {
                    id: true,
                    artist: true,
                    album: true,
                    title: true,
                    duration: true,
                    genre: true
                },
                TagsData: {
                    title: "Название",
                    disc_no: "Диск №",
                    disc_of: "из",
                    track_no: "Композиция №",
                    track_of: "из",
                    genre: "Жанр",
                    artist: "Исполнитель", 
                    album: "Альбом",
                    year: "Год"
                },
                urlsData: new Map(),
            }
        },
        computed: {
            preparedTracks() {
                if (!this.FindTracks || !Array.isArray(this.FindTracks)) {
                    return [];
                }

                return this.FindTracks.map((track, index) => {
                    if (!track || typeof track !== 'object') {
                        console.warn('Invalid track object::', index);
                        return null;
                    }
                    
                    const trackId = this.getStableTrackId(track, index);
                    const trackKey = `${track.name}-${track.size}`;
                    let finalObj = {
                        id: trackId,
                        name: track.name || 'Unknown',
                        common: track.common || {},
                        duration: this.audioDurations[trackKey] || null,
                        url: this.getObjectURL(track),
                        key: trackKey
                    };

                    return finalObj;
                }).filter(track => track !== null);
            },
            currentTrack() {
                const curindex = this.currentIndex + '';
                const preplist = this.preparedTracks;

                if (preplist && preplist.length > 0 && curindex) {
                    return preplist[curindex];
                } else {
                    return null;
                }
            },
        },
        watch: {
            FindTracks: {
                handler(newTracks) {
                    if (newTracks && newTracks.length > 0) this.preloadDurationsSimple(newTracks);
                },
                immediate: true //делать сразу, при создании наблюдателя (без него - будет ждать послед. изменения)
            },
        },
        methods: {
            getStableTrackId(track, index) {
                if (track.id) return track.id;
                
                // Простая генерация ID
                return `track-${index}-${Date.now()}-${Math.random().toString(36).substr(2, 9)}`;
            },
            getObjectURL(track) {
                const key = `${track.name}-${track.size}`;
                if (!this.urlsData.has(key)) {
                    this.urlsData.set(key, URL.createObjectURL(track));
                }
                return this.urlsData.get(key);
            },
            SelectedTrack(trackindex) {
                if (!this.currentIndex || this.currentIndex !== trackindex) {
                    this.currentIndex = trackindex;
                    // this.$emit("selected-track", track);
                }
            },
            GetRandom_Ind () {
                const maxVal = (this.preparedTracks) ? this.preparedTracks.length : 0;
                return Math.floor(Math.random() * maxVal);
            },
            // SelectedTrack(track) {
            //     if (!this.currentTrack || this.currentTrack.id !== track.id) {
            //         this.currentTrack = track;
            //         // this.$emit("selected-track", track);
            //     }
            // },
            onFilesSelected(files) {
                this.FindTracks = files;
                // this.FindTracks = files.filter(file => file.type === 'audio/mpeg');
                // console.log("FindTracks", this.FindTracks);
            },
            PlayerAction_Play() {
                const AudioElement = document.querySelector('#main_audio');
                const preplist = this.preparedTracks;

                if (AudioElement && preplist && preplist.length > 0) {
                    AudioElement.play();
                    this.Play = true;
                }                
            },
            PlayerAction_Pause() {
                const AudioElement = document.querySelector('#main_audio');
                const preplist = this.preparedTracks;

                if (AudioElement && preplist && preplist.length > 0) {
                    AudioElement.pause();
                    this.Play = false;
                }
            },
            PlayerAction_VolPlus() {
                const AudioElement = document.querySelector('#main_audio');

                if (AudioElement) {
                    if (this.VolumeLvl < 1) {
                        this.VolumeLvl = parseFloat((this.VolumeLvl + 0.1).toFixed(1));
                    }
                    AudioElement.volume = this.VolumeLvl;
                }
            },
            PlayerAction_VolMinus() {
                const AudioElement = document.querySelector('#main_audio');
                if (this.VolumeLvl > 0) {
                    this.VolumeLvl = parseFloat((this.VolumeLvl - 0.1).toFixed(1));
                }
                AudioElement.volume = this.VolumeLvl;
            },
            PlayerAction_PrevTrk() {
                // const AudioElement = document.querySelector('#main_audio');
                const curindex = this.currentIndex;
                const preplist = this.preparedTracks;

                if (preplist && preplist.length > 0) {
                    this.PlayerAction_Pause();

                    if (this.Shuffle) {
                        this.currentIndex = this.GetRandom_Ind();
                        console.log("new curindex", this.Shuffle);
                    } else if (curindex == 0) {
                        this.currentIndex = preplist.length - 1;
                    } else {
                        this.currentIndex = curindex - 1;
                    }


                    setTimeout(() => {
                        this.PlayerAction_Play();
                    }, 500);
                }
            },
            PlayerAction_NextTrk() {
                // const AudioElement = document.querySelector('#main_audio');
                const curindex = this.currentIndex;
                const preplist = this.preparedTracks;

                if (preplist && preplist.length > 0) {
                    this.PlayerAction_Pause();

                    if (this.Shuffle) {
                        this.currentIndex = this.GetRandom_Ind();
                        console.log("new curindex", this.Shuffle);
                    } else if (curindex == (preplist.length - 1)) {
                        this.currentIndex = 0;
                    } else {
                        this.currentIndex = curindex + 1;
                    }

                    setTimeout(() => {
                        this.PlayerAction_Play();
                    }, 500);
                    
                }
            },
            Chng_Shuffle() {
                this.Shuffle = !this.Shuffle;
            },
            Chng_SilentMode() {
                const Silent = this.SilentMode;

                if (!Silent) {
                    this.VolumeLvl_save = this.VolumeLvl;
                    this.VolumeLvl = 0;
                } else {
                    this.VolumeLvl = this.VolumeLvl_save;
                }

                this.SilentMode = !Silent;
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
                        this.RepeatMode = "all";
                        AudioElement.loop = false;
                        break;
                }
            },
            Chng_TrackRangeTime() {
                const AudioElement = document.querySelector('#main_audio');
                const TrackTimeElement = document.querySelector('#main_tracktime');

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
            },
            Chng_tableShow() {
                this.tableShow = (this.tableShow == 0) ? 1 : 0;
            },
            Upd_tableShow() {
                let newConfig = this.showTagsData;
                // Object.keys(newConfig)
                for (var key in newConfig) {
                    newConfig[key] = document.querySelector('#tabTag_' + key).checked;
                }
                
                this.showTagsData = newConfig;
                this.Chng_tableShow();
            },
            onAudioEnded() {
                const RMode = this.RepeatMode;
            
                if (RMode === 'one') {
                    this.PlayerAction_Play();
                } else {
                    this.PlayerAction_NextTrk();
                }
                
            },
            formatDuration(seconds) {
                if (!seconds || isNaN(seconds)) {
                    return '00:00';
                } else {
                    return `${Math.floor(seconds / 60).toString().padStart(2, '0')}:${Math.floor(seconds % 60).toString().padStart(2, '0')}`;
                }
            },
            // вычисление длительности файла
            getAudioDurationSimple(track) {
                return new Promise((resolve, reject) => {
                    const audio = new Audio();
                    const objectURL = URL.createObjectURL(track);
                    
                    audio.src = objectURL;
                    
                    const mkClean = () => {
                        audio.removeEventListener('loadedmetadata', onLoaded);
                        audio.removeEventListener('error', onError);
                        URL.revokeObjectURL(objectURL); // Освобождаем ссылку в памяти "URL" - сразу
                    };
                    
                    // чистим ссылки, достаем длительность
                    const onLoaded = () => {
                        mkClean();
                        resolve(audio.duration);
                    };
                    // чистим ссылки, возвращаем ошибку
                    const onError = (e) => {
                        mkClean();
                        reject(e);
                    };
                    
                    audio.addEventListener('loadedmetadata', onLoaded); //подписка на событие "загрузка мета-данных"
                    audio.addEventListener('error', onError); //подписка на событие "ошибка"
                    
                    // Таймаут
                    setTimeout(() => {
                        mkClean();
                        reject(new Error('Timeout loading audio metadata'));
                    }, 3000);
                });
            },
            async preloadDurationsSimple(tracks) {
                for (const track of tracks) {
                    const trackKey = `${track.name}-${track.size}`;
                    
                    if (!this.audioDurations[trackKey]) {
                        try {
                            this.audioDurations[trackKey] = await this.getAudioDurationSimple(track);
                        } catch (error) {
                            console.warn(`Не удалось получить длительность для ${track.name}:`, error);
                            this.audioDurations[trackKey] = 0;
                        }
                    }
                }
            },
        }
    }
</script>