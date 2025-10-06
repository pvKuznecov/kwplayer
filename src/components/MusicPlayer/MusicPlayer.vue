<template src="./MusicPlayer.html"></template>
<style src="./MusicPlayer.css"></style>
<script>
    export default {
        name: 'MusicPlayer',
        data() {
            return {
                STrack: false,
            }
        },
        props: ['tracks'], // Переданные нам треки
        computed: {
            preparedTracks() {
                console.log("tracks", this.tracks);

                return this.tracks.map(track => ({
                    id: track.id || track.name, // Используйте ID или имя, если ID нет
                    name: track.name,           // Название трека
                    url: URL.createObjectURL(track) // Создаем URL для воспроизведения
                }));
            },
        },
        methods: {
            SelectedTrack(trVal) {
                console.log("trVal", trVal);
                this.STrack = trVal;
                this.$emit("selected-track", trVal);
            }
        }
    };
</script>