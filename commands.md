Download a youtube video/playlist/channel and convert it to a low quality mp3:
```
youtube-dl --extract-audio --audio-format mp3 --audio-quality 9 --ignore-errors --download-archive downloaded.txt
```

Generate the linux hash of a password using sha512
```
python -c 'import crypt; print crypt.crypt("password", "$6$random_salt")'
```
