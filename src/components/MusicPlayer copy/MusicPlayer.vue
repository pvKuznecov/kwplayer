<template src="./MusicPlayer.html"></template>
<style src="./MusicPlayer.css"></style>
<script>
    export default {
        name: 'MusicPlayer',
        data() {
            return {
                currentTrack: false,
            }
        },
        props: ['tracks'], // Переданные нам треки
        computed: {
            preparedTracks() {
                return this.tracks.map((track) => ({
                    id: track.id || Math.random().toString(36).substr(2, 9),
                    name: track.name,
                    common: track.common,
                    url: URL.createObjectURL(track) // Создаем URL для воспроизведения
                }));
            },
        },
        methods: {
            SelectedTrack(track) {
                if (this.currentTrack !== track) {   // проверяем, изменился ли выбор
                    this.currentTrack = track;
                    this.$emit("selected-track", track);
                }
            }
        }
    };
</script>