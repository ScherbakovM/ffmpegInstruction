# Руководство по FFmpeg

## Содержание
1. [Что такое FFmpeg](#что-такое-ffmpeg)
2. [Установка FFmpeg](#установка-ffmpeg)
3. [Базовый синтаксис команд](#базовый-синтаксис-команд)
4. [Основные команды](#основные-команды)
5. [Работа с видео](#работа-с-видео)
6. [Работа с аудио](#работа-с-аудио)
7. [Продвинутые техники](#продвинутые-техники)
8. [Полезные примеры](#полезные-примеры)
9. [Обновление FFmpeg](#обновление-ffmpeg)
10. [Решение проблем](#решение-проблем)

---

## Что такое FFmpeg

FFmpeg — это мощная кроссплатформенная библиотека и набор инструментов командной строки для записи, конвертации и потоковой передачи аудио и видео. FFmpeg поддерживает практически все существующие мультимедийные форматы и кодеки.

### Основные возможности:
- Конвертация между форматами видео и аудио
- Изменение разрешения, битрейта, частоты кадров
- Извлечение аудио из видео
- Создание GIF из видео
- Обрезка и склеивание файлов
- Добавление субтитров и водяных знаков
- Потоковая передача мультимедиа

---

## Установка FFmpeg

### Windows

#### Способ 1: Официальная загрузка
1. Перейдите на [ffmpeg.org](https://ffmpeg.org/download.html)
2. Выберите "Windows" → "Windows builds by BtbN"
3. Скачайте архив ffmpeg-master-latest-win64-gpl.zip
4. Распакуйте архив в папку C:\ffmpeg
5. Добавьте C:\ffmpeg\bin в переменную PATH:
   - Нажмите Win + R, введите sysdm.cpl
   - Перейдите на вкладку "Дополнительно"
   - Нажмите "Переменные среды"
   - В разделе "Системные переменные" найдите PATH
   - Нажмите "Изменить" → "Создать" → добавьте C:\ffmpeg\bin
   - Нажмите "ОК" во всех окнах

#### Способ 2: Через Chocolatey
# Установите Chocolatey (если не установлен)
# Затем выполните:
choco install ffmpeg
#### Способ 3: Через Scoop
# Установите Scoop (если не установлен)
# Затем выполните:
scoop install ffmpeg
### macOS

#### Способ 1: Через Homebrew (рекомендуется)
# Установите Homebrew (если не установлен)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Установите FFmpeg
brew install ffmpeg
#### Способ 2: Через MacPorts
sudo port install ffmpeg
### Linux

#### Ubuntu/Debian
# Обновите список пакетов
sudo apt update

# Установите FFmpeg
sudo apt install ffmpeg

# Проверьте установку
ffmpeg -version
#### CentOS/RHEL/Fedora
# Для CentOS/RHEL (требует EPEL репозитория)
sudo yum install epel-release
sudo yum install ffmpeg ffmpeg-devel

# Для Fedora
sudo dnf install ffmpeg ffmpeg-devel
#### Arch Linux
sudo pacman -S ffmpeg
### Проверка установки
ffmpeg -version
Вы должны увидеть информацию о версии FFmpeg и поддерживаемых кодеках.

---

## Базовый синтаксис команд

### Общая структура команды:
ffmpeg [глобальные_опции] [входные_опции] -i входной_файл [выходные_опции] выходной_файл
### Основные компоненты:
- -i — указывает входной файл
- -c — указывает кодек (codec)
- -f — указывает формат
- -y — перезаписать выходной файл без запроса
- -n — не перезаписывать существующий файл

### Пример базовой команды:
ffmpeg -i input.mp4 -c:v libx264 -c:a aac output.mp4
---

## Основные команды

### Получение информации о файле
# Подробная информация о файле
ffmpeg -i video.mp4

# Только основная информация
ffprobe video.mp4

# Информация в JSON формате
ffprobe -v quiet -print_format json -show_format -show_streams video.mp4
### Простая конвертация
# Конвертация между форматами
ffmpeg -i input.avi output.mp4
ffmpeg -i input.mp4 output.mkv
ffmpeg -i input.mov output.webm
### Конвертация с сохранением качества
# Копирование потоков без перекодирования (быстро)
ffmpeg -i input.mp4 -c copy output.mkv

# Конвертация только контейнера
ffmpeg -i input.avi -c:v copy -c:a copy output.mp4
---

## Работа с видео

### Изменение разрешения
`bash
# Изменить разрешение на 1280x720
ffmpeg -i input.mp4 -vf scale=1280:720 output.mp4

# Изменить разрешение с сохранением пропорций
ffmpeg -i input.mp4 -vf scale=1280:-1 output.mp4

# Изменить разрешение на 50% от оригинала
ffmpeg -i input.mp4 -vf scale=iw*0.5:ih*0.5 output.mp4

### Изменение частоты кадров (FPS)
bash
# Установить FPS 30
ffmpeg -i input.mp4 -r 30 output.mp4

# Установить FPS 60 с интерполяцией
ffmpeg -i input.mp4 -filter:v fps=60 output.mp4

### Изменение битрейта
bash
# Установить битрейт видео 2000k
ffmpeg -i input.mp4 -b:v 2000k output.mp4

# Установить битрейт аудио 128k
ffmpeg -i input.mp4 -b:a 128k output.mp4

# Установить оба битрейта
ffmpeg -i input.mp4 -b:v 2000k -b:a 128k output.mp4

### Обрезка видео
bash
# Обрезать с 30 секунды длительностью 60 секунд
ffmpeg -i input.mp4 -ss 00:00:30 -t 00:01:00 output.mp4

# Обрезать с начала до 2 минут
ffmpeg -i input.mp4 -t 00:02:00 output.mp4

# Обрезать с 1 минуты до конца
ffmpeg -i input.mp4 -ss 00:01:00 output.mp4

### Кадрирование (cropping)
bash
# Кадрировать область 640x480 начиная с координат (200,100)
ffmpeg -i input.mp4 -vf crop=640:480:200:100 output.mp4

# Автоматическое обнаружение черных полос и кадрирование
ffmpeg -i input.mp4 -vf cropdetect -f null -
# Затем используйте полученные значения
ffmpeg -i input.mp4 -vf crop=1920:800:0:140 output.mp4

### Поворот видео
bash
# Поворот на 90 градусов по часовой стрелке
ffmpeg -i input.mp4 -vf transpose=1 output.mp4

# Поворот на 90 градусов против часовой стрелки
ffmpeg -i input.mp4 -vf transpose=2 output.mp4

# Поворот на 180 градусов
ffmpeg -i input.mp4 -vf transpose=2,transpose=2 output.mp4

### Создание GIF из видео
bash
# Простое создание GIF
ffmpeg -i input.mp4 -vf scale=320:-1 -r 10 output.gif

# GIF с оптимизацией палитры (лучшее качество)
ffmpeg -i input.mp4 -vf "fps=10,scale=320:-1:flags=lanczos,palettegen" palette.png
ffmpeg -i input.mp4 -i palette.png -filter_complex "fps=10,scale=320:-1:flags=lanczos[x];[x][1:v]paletteuse" output.gif

---

## Работа с аудио

### Извлечение аудио из видео
bash
# Извлечь аудио в MP3
ffmpeg -i input.mp4 -vn -acodec mp3 -ab 192k output.mp3

# Извлечь аудио в WAV
ffmpeg -i input.mp4 -vn -acodec pcm_s16le output.wav

# Извлечь аудио без перекодирования
ffmpeg -i input.mp4 -vn -acodec copy output.aac

### Конвертация аудио форматов
bash
# MP3 в WAV
ffmpeg -i input.mp3 output.wav

# WAV в FLAC
ffmpeg -i input.wav -c:a flac output.flac

# MP3 в AAC
ffmpeg -i input.mp3 -c:a aac -b:a 128k output.aac

### Изменение громкости
bash
# Увеличить громкость в 2 раза
ffmpeg -i input.mp3 -af volume=2.0 output.mp3

# Уменьшить громкость на 50%
ffmpeg -i input.mp3 -af volume=0.5 output.mp3

# Увеличить на 10 дБ
ffmpeg -i input.mp3 -af volume=10dB output.mp3

### Обрезка аудио
bash
# Обрезать с 30 секунды длительностью 60 секунд
ffmpeg -i input.mp3 -ss 00:00:30 -t 00:01:00 output.mp3

# Fade in и fade out эффекты
ffmpeg -i input.mp3 -af "afade=t=in:ss=0:d=3,afade=t=out:st=57:d=3" output.mp3

---

## Продвинутые техники

### Склеивание файлов
bash
# Создайте текстовый файл list.txt:
# file 'video1.mp4'
# file 'video2.mp4'
# file 'video3.mp4'

ffmpeg -f concat -safe 0 -i list.txt -c copy output.mp4

### Добавление субтитров
bash
# Встроить субтитры в видео
ffmpeg -i input.mp4 -i subtitles.srt -c copy -c:s mov_text output.mp4

# Наложить субтитры поверх видео
ffmpeg -i input.mp4 -vf subtitles=subtitles.srt output.mp4

### Добавление водяного знака
bash
# Добавить изображение как водяной знак
ffmpeg -i input.mp4 -i watermark.png -filter_complex "overlay=W-w-10:H-h-10" output.mp4

# Добавить текстовый водяной знак
ffmpeg -i input.mp4 -vf drawtext="fontfile=/path/to/font.ttf:text='Watermark':fontcolor=white:fontsize=24:box=1:boxcolor=black@0.5:boxborderw=5:x=w-tw-10:y=h-th-10" output.mp4

### Создание миниатюр
bash
# Создать одну миниатюру на 10 секунде
ffmpeg -i input.mp4 -ss 00:00:10 -vframes 1 thumbnail.jpg

# Создать миниатюры каждые 60 секунд
ffmpeg -i input.mp4 -vf fps=1/60 thumb%04d.jpg

### Работа с несколькими файлами
bash
# Создать видео side-by-side
ffmpeg -i left.mp4 -i right.mp4 -filter_complex hstack output.mp4

Михаил Щербаков, [20.06.2025 1:54]
# Создать видео picture-in-picture
ffmpeg -i main.mp4 -i overlay.mp4 -filter_complex "[1:v]scale=320:240[ovrl];[0:v][ovrl]overlay=W-w-10:H-h-10" output.mp4

---

## Полезные примеры

### Оптимизация для веб
bash
# Создание веб-оптимизированного MP4
ffmpeg -i input.mp4 -c:v libx264 -preset slow -crf 22 -c:a aac -b:a 128k -movflags +faststart output.mp4

### Создание видео из изображений
bash
# Создать видео из последовательности изображений
ffmpeg -framerate 30 -i image%03d.jpg -c:v libx264 -r 30 -pix_fmt yuv420p output.mp4

# Создать слайдшоу с переходами
ffmpeg -framerate 1/3 -i image%03d.jpg -vf "fade=t=in:st=0:d=0.5,fade=t=out:st=2.5:d=0.5" -c:v libx264 -r 30 slideshow.mp4

### Стриминг
bash
# Стрим на YouTube Live
ffmpeg -re -i input.mp4 -c:v libx264 -preset veryfast -maxrate 3000k -bufsize 6000k -pix_fmt yuv420p -g 50 -c:a aac -b:a 160k -ac 2 -ar 44100 -f flv rtmp://a.rtmp.youtube.com/live2/YOUR_STREAM_KEY

# Стрим на Twitch
ffmpeg -re -i input.mp4 -c:v libx264 -preset veryfast -maxrate 3000k -bufsize 6000k -pix_fmt yuv420p -g 50 -c:a aac -b:a 160k -ac 2 -ar 44100 -f flv rtmp://live.twitch.tv/live/YOUR_STREAM_KEY

### Конвертация для мобильных устройств
bash
# Оптимизация для мобильных устройств
ffmpeg -i input.mp4 -c:v libx264 -profile:v baseline -level 3.0 -preset slow -crf 22 -maxrate 1000k -bufsize 2000k -vf scale=854:480 -c:a aac -b:a 128k -ac 2 output_mobile.mp4

---

## Обновление FFmpeg

### Windows
bash
# Через Chocolatey
choco upgrade ffmpeg

# Через Scoop
scoop update ffmpeg

### macOS
bash
# Через Homebrew
brew upgrade ffmpeg

### Linux
bash
# Ubuntu/Debian
sudo apt update && sudo apt upgrade ffmpeg

# CentOS/RHEL
sudo yum update ffmpeg

# Fedora
sudo dnf upgrade ffmpeg

# Arch Linux
sudo pacman -Syu ffmpeg

### Сборка из исходного кода (для получения последней версии)
bash
# Клонирование репозитория
git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg
cd ffmpeg

# Конфигурация и сборка
./configure --enable-gpl --enable-libx264 --enable-libx265 --enable-libfdk-aac --enable-libmp3lame --enable-libopus --enable-libvorbis
make -j$(nproc)
sudo make install

---

## Решение проблем

### Частые ошибки и их решения

#### "Permission denied" или "Access denied"
bash
# Используйте полные пути к файлам
ffmpeg -i "/full/path/to/input.mp4" "/full/path/to/output.mp4"

# Проверьте права доступа к файлам
chmod 644 input.mp4

#### "No such file or directory"
bash
# Проверьте, что FFmpeg установлен
which ffmpeg

# Проверьте, что файл существует
ls -la input.mp4

#### Проблемы с кодеками
bash
# Проверьте доступные кодеки
ffmpeg -codecs | grep -i h264

# Используйте альтернативные кодеки
ffmpeg -i input.mp4 -c:v libx265 output.mp4  # вместо libx264

#### Проблемы с аудио
bash
# Проверьте аудио потоки
ffprobe -v quiet -show_streams -select_streams a input.mp4

# Принудительно установите параметры аудио
ffmpeg -i input.mp4 -c:a aac -ar 44100 -ac 2 output.mp4

### Полезные диагностические команды
bash
# Проверка конфигурации FFmpeg
ffmpeg -version
ffmpeg -formats
ffmpeg -codecs
ffmpeg -filters

# Подробный вывод для отладки
ffmpeg -v debug -i input.mp4 output.mp4

# Показать только ошибки
ffmpeg -v error -i input.mp4 output.mp4

### Оптимизация производительности
bash
# Использование аппаратного ускорения (NVIDIA)
ffmpeg -hwaccel cuda -i input.mp4 -c:v h264_nvenc output.mp4

# Многопоточность
ffmpeg -threads 0 -i input.mp4 output.mp4  # 0 = автоматически определить количество ядер

# Оптимизация пресетов
ffmpeg -i input.mp4 -c:v libx264 -preset ultrafast output.mp4  # быстро, но больший размер
ffmpeg -i input.mp4 -c:v libx264 -preset slow output.mp4      # медленно, но меньший размер
`

---

## Заключение

FFmpeg — мощный инструмент для работы с мультимедиа. Это руководство покрывает основные случаи использования, но возможности FFmpeg практически безграничны. Для изучения дополнительных функций рекомендуется:

1. Изучить официальную документацию: [ffmpeg.org/documentation.html](https://ffmpeg.org/documentation.html)
2. Экспериментировать с различными фильтрами и опциями
3. Использовать ffmpeg -h для получения справки по командам
4. Присоединиться к сообществу FFmpeg для получения помощи

Помните: всегда делайте резервные копии важных файлов перед их обработкой!
