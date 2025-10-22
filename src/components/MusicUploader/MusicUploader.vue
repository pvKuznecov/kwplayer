<template src="./MusicUploader.html"></template>
<style src="./MusicUploader.css"></style>
<script>
    import { parseBlob } from 'music-metadata';
    
    export default {
        name: 'MusicUploader',
        data() {
            return {
                PlayList: false,
                isLoading: false,
                totalFiles: 0,
                processedCount: 0,
                MAX_FILE_SIZE: 100 * 1024 * 1024, // 100MB
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

                this.isLoading = true;
                this.totalFiles = audioFiles.length;
                this.processedCount = 0;

                let validFiles = [];
        
                // Проходим по каждому файлу и проверяем его тип
                for (let file of files) {
                    if (!this.isAudio(file)) {
                        console.log(`Файл ${file.name} не является аудиофайлом.`);
                        continue; // Переходим к следующему файлу
                    }

                    
                    try {
                        const blob = file.slice(0, file.size); 
                        let parsedData = await parseBlob(blob);                        
                        
                        file.common = parsedData.common;
                    } catch (error) {
                        console.error('Ошибка при разборе файла:', error)
                    }
                    
                    validFiles.push(file); // Добавляем подходящий файл в массив
                    this.processedCount++;

                    if (this.processedCount == this.totalFiles) {
                        this.isLoading = false;
                    }
                }

                // Если были выбраны правильные файлы, отправляем их вверх
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