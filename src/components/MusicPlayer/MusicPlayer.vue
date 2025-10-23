<template src="./MusicPlayer.html"></template>
<style src="./MusicPlayer.css"></style>
<script>
// import { handleError } from 'vue';

export default {
    name: 'MusicPlayer',
    data() {
        return {
            currentTrack: null,
            audioDurations: {}, // Объект для "длительностей"
            tableShowConfig: {
                id: true,
                artist: true,
                album: true,
                title: true,
                duration: true
            },
            urlsData: new Map(),
        }
    },
    props: {
        tracks: { type: Array, default: () => [] },
        tableshow: { type: Object, default: () => ({}) },
    },
    computed: {
        preparedTracks() {
            if (!this.tracks || !Array.isArray(this.tracks)) {
                return [];
            }

            return this.tracks.map((track, index) => {
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
    },
    watch: {
        tracks: {
            handler(newTracks) {
                if (newTracks && newTracks.length > 0) this.preloadDurationsSimple(newTracks);
            },
            immediate: true //делать сразу, при создании наблюдателя (без него - будет ждать послед. изменения)
        },
        tableshow: {
            handler(newObj) {
                if (newObj && newObj !== null && typeof newObj === 'object' && newObj !== this.tableShowConfig) {
                    this.tableShowConfig = newObj;
                }
            },
            immediate: true
        }
    },
    methods: {
        getStableTrackId(track, index) {
            if (track.id) return track.id;
            
            // Простая генерация ID
            return `track-${index}-${Date.now()}-${Math.random().toString(36).substr(2, 9)}`;
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
        formatDuration(seconds) {
            if (!seconds || isNaN(seconds)) {
                return '00:00';
            } else {
                return `${Math.floor(seconds / 60).toString().padStart(2, '0')}:${Math.floor(seconds % 60).toString().padStart(2, '0')}`;
            }
        },
        SelectedTrack(track) {
            if (!this.currentTrack || this.currentTrack.id !== track.id) {
                this.currentTrack = track;
                this.$emit("selected-track", track);
            }
        },
        getObjectURL(track) {
            const key = `${track.name}-${track.size}`;
            if (!this.urlsData.has(key)) {
                this.urlsData.set(key, URL.createObjectURL(track));
            }
            return this.urlsData.get(key);
        },
    },
    beforeUnmount() {
        for (const url of this.urlsData.values()) {
            URL.revokeObjectURL(url);
        }
        this.urlsData.clear();
    }
};
</script>