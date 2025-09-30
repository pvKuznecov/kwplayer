<template src="./MainView.html"></template>
<style src="./MainView.css"></style>
<script>
    import MusicPlayer from '@/components/MusicPlayer/MusicPlayer.vue';
    import MusicUploader from '@/components/MusicUploader/MusicUploader.vue';

    export default {
        name: 'MainView',
        components: {
            MusicUploader,
            MusicPlayer
        },
        data() {
            return {
                selectedTracks: []
            };
        },
        methods: {
            onFilesSelected(files) {
                this.selectedTracks = files.filter(file => file.type === 'audio/mp3');
            }
        }
    };
</script>