<template src="./MusicPlayer.html"></template>
<style src="./MusicPlayer.css"></style>
<script>
export default {
    name: 'MusicPlayer',
    data() {
        return {
            currentTrack: null,
            audioDurations: {} // Простой объект для длительностей
        }
    },
    props: {
        tracks: { type: Array, default: () => [] }
    },
    computed: {
        preparedTracks() {
            if (!this.tracks || !Array.isArray(this.tracks)) {
                return [];
            }
            
            let resArr = this.tracks.map((track, index) => {
                if (!track || typeof track !== 'object') {
                    console.warn('Invalid track object::', index);
                    return null;
                }
                
                const trackId = this.getStableTrackId(track, index);
                const trackKey = this.getTrackKey(track);
                let finalObj = {
                    id: trackId,
                    name: track.name || 'Unknown',
                    common: track.common || {},
                    duration: this.audioDurations[trackKey] || null,
                    url: URL.createObjectURL(track),
                    key: trackKey
                };

                return finalObj;
            }).filter(track => track !== null);

            return resArr;
        },
    },
    watch: {
        tracks: {
            handler(newTracks) {
                if (newTracks && newTracks.length > 0) {
                    this.preloadDurationsSimple(newTracks);
                }
            },
            immediate: true //делать сразу, при создании наблюдателя (без него - будет ждуть послед. изменения)
        }
    },
    methods: {
        getStableTrackId(track, index) {
            if (track.id) return track.id;
            
            // Простая генерация ID
            return `track-${index}-${Date.now()}-${Math.random().toString(36).substr(2, 9)}`;
        },

        getTrackKey(track) {
            return `${track.name}-${track.size}`;
        },

        async preloadDurationsSimple(tracks) {
            for (const track of tracks) {
                const trackKey = this.getTrackKey(track);
                
                // Если длительность еще не загружена
                if (!this.audioDurations[trackKey]) {
                    try {
                        const duration = await this.getAudioDurationSimple(track);
                        // Простое присваивание - Vue 3 реактивно обновит это
                        this.audioDurations[trackKey] = duration;
                    } catch (error) {
                        console.warn(`Не удалось получить длительность для ${track.name}:`, error);
                        this.audioDurations[trackKey] = 0;
                    }
                }
            }
        },

        getAudioDurationSimple(track) {
            return new Promise((resolve, reject) => {
                const audio = new Audio();
                const objectURL = URL.createObjectURL(track);
                
                audio.src = objectURL;
                
                const cleanup = () => {
                    audio.removeEventListener('loadedmetadata', onLoaded);
                    audio.removeEventListener('error', onError);
                    URL.revokeObjectURL(objectURL); // Освобождаем URL сразу
                };
                
                const onLoaded = () => {
                    cleanup();
                    resolve(audio.duration);
                };
                
                const onError = (e) => {
                    cleanup();
                    reject(e);
                };
                
                audio.addEventListener('loadedmetadata', onLoaded);
                audio.addEventListener('error', onError);
                
                // Таймаут
                setTimeout(() => {
                    cleanup();
                    reject(new Error('Timeout loading audio metadata'));
                }, 3000);
            });
        },

        formatDuration(seconds) {
            if (!seconds || isNaN(seconds)) return '00:00';
            
            const mins = Math.floor(seconds / 60);
            const secs = Math.floor(seconds % 60);
            return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
        },

        SelectedTrack(track) {
            if (!this.currentTrack || this.currentTrack.id !== track.id) {
                this.currentTrack = track;
                this.$emit("selected-track", track);
            }
        }
    }
};
</script>