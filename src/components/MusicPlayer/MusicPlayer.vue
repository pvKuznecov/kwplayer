<template src="./MusicPlayer.html"></template>
<style src="./MusicPlayer.css"></style>
<script>
    export default {
        name: 'MusicPlayer',
        props: ['tracks'], // Переданные нам треки
        computed: {
            preparedTracks() {
                return this.tracks.map(track => ({
                    id: track.id || track.name, // Используйте ID или имя, если ID нет
                    name: track.name,           // Название трека
                    url: URL.createObjectURL(track) // Создаем URL для воспроизведения
                }));
            },
        },
    };
</script>