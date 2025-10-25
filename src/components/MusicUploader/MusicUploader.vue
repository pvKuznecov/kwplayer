<template src="./MusicUploader.html"></template>
<style src="./MusicUploader.css"></style>
<script>
    import { parseBlob } from 'music-metadata';

    // const screenWidth = window.screen.width;
    // const screenHeight = window.screen.height;
    // console.log(`Разрешение экрана: ${screenWidth}x${screenHeight}`);
    
    export default {
        name: 'MusicUploader',
        data() {
            return {
                PlayList: false,
                isLoading: false,
                totalFiles: 0,
                processedCount: 0,
                MAX_FILE_SIZE: 100 * 1024 * 1024, // 100MB,
                MAX_PLAYLIST_SIZE: 1000 * 1024 * 1024, // 1000MB,
                curFilesSize: 0,
            }
        },
        methods: {
            async handleFileSelect(event) {
                const files = Array.from(event.target.files);
                const audioFiles = files.filter(file => this.isAudio(file));

                if (audioFiles.length === 0) {
                    this.showError("Не было выбрано ни одного аудиофайла.");
                    this.resetInput(event.target);
                    return;
                }

                let cSize = 0;
                files.forEach(function(item) { cSize += item.size; })

                if (cSize > this.MAX_PLAYLIST_SIZE) {
                    this.showError("Превышен лимит доступной памяти! Максимальный размер - " + (this.MAX_PLAYLIST_SIZE / 1024 / 1024) + "MB.");
                    this.resetInput(event.target);
                    return;
                }

                this.isLoading = true;
                this.totalFiles = audioFiles.length;
                this.processedCount = 0;

                let validFiles = [];

                for (let afile of audioFiles) {
                    try {
                        const blob = afile.slice(0, afile.size);
                        let parsedData = await parseBlob(blob);

                        afile.common = parsedData.common;
                        validFiles.push(afile); // Добавляем подходящий файл в массив
                    } catch (error) {
                        console.error('Ошибка при разборе файла:', error);
                    }
                    this.processedCount++;
                }

                this.isLoading = false;

                if (validFiles.length > 0) {
                    this.$emit('files-selected', validFiles);
                    this.PlayList = true;
                } else {
                    alert("Не было выбрано ни одного аудиофайла.");
                }
            },
            isAudio(file) {
                if (file.size > this.MAX_FILE_SIZE) {
                    console.warn(`Файл ${file.name} слишком большой: ${file.size} bytes`);
                    return false;
                }

                // Более надежная проверка аудиофайлов
                const audioTypes = [ 'audio/mpeg', 'audio/wav', 'audio/flac', 'audio/ogg', 'audio/aac', 'audio/x-m4a' ];
                const extension = file.name.toLowerCase().split('.').pop();
                const audioExtensions = ['mp3', 'wav', 'flac', 'ogg', 'aac', 'm4a'];
            
                return audioTypes.includes(file.type) || audioExtensions.includes(extension);
            },
            showError(message) {
                alert(message);
                console.error(message);
            },
            // Сброс input (открывает возможность повторно загружать те-же файлы)
            resetInput(inputElement) {
                inputElement.value = '';
            }
        }
    }
</script>