<template src="./MusicUploader.html"></template>
<style src="./MusicUploader.css"></style>
<script>
    export default {
        name: 'MusicUploader',
        data() {
            return {
                PlayList: false
            }
        },
        methods: {
            handleFileSelect(event) {
                const files = event.target.files;
                this.$emit('files-selected', Array.from(files));
                this.PlayList = true;
            }
        }
    }
</script>