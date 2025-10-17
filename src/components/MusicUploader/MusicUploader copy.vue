<template src="./MusicUploader.html"></template>
<style src="./MusicUploader.css"></style>
<script>
    export default {
        name: 'MusicUploader',
        data() {
            return {
                PlayList: false,
            }
        },
        methods: {
            handleFileSelect(event) {
                const files = event.target.files;
                let validFiles = [];
        
                // Проходим по каждому файлу и проверяем его тип
                for (let file of files) {
                    if (!this.isAudio(file)) {
                        console.log(`Файл ${file.name} не является аудиофайлом.`);
                        continue; // Переходим к следующему файлу
                    }
                    
                    validFiles.push(file); // Добавляем подходящий файл в массив
                }

                // Если были выбраны правильные файлы, отправляем их вверх
                if (validFiles.length > 0) {
                    this.$emit('files-selected', validFiles);
                    this.PlayList = true;
                } else {
                    alert("Не было выбрано ни одного аудиофайла.");
                }
            },
            // Проверяем MIME-type файла на соответствие типу audio
            isAudio(file) {
                return new RegExp("^audio/", "i").test(file.type);
            }
        }
    }
</script>