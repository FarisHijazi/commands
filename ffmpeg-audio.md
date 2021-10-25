## Handy `ffmpeg` commands

You can use this for `ffmpeg` things other than conversion of course (be sure to change `srcext` to the source extension)

```bash
# segments and converts audios with progressbar
# `cd` to the parent directory of the audio files then run the command
srcext=ogg; find . -type f -name "*.$srcext" -print | xargs -P $(nproc --all) -I{} sh -c 'ffmpeg -n -hide_banner -loglevel error -i "$1" -ar 22050 -ac 1 -f segment -segment_time 10 "${1%.*}.seg%03d.22050.wav" | echo "" ' -- {} | tqdm --unit .$srcext --total $(find -type f -name "*.$srcext" | wc -l) > /dev/null

## just resample without segmenting
srcext=ogg; find . -type f -name "*.$srcext" -print | xargs -P $(nproc --all) -I{} sh -c 'ffmpeg -n -hide_banner -loglevel error -i "$1" -ar 22050 -ac 1  "${1%.*}.22050.wav" | echo "" ' -- {} | tqdm --unit .$srcext --total $(find -type f -name "*.$srcext" | wc -l) > /dev/null

# then after that you could delete the other .wav files like so:
find . -not -name "*.22050.wav" -name "*.wav" -type f -delete
```

**read durations of `.wav` files** (be sure to change `srcext` to the source extension)

```bash
# **read durations of .wav files** (be sure to change srcext to the source extension)
srcext=wav; find -type f -name "*.$srcext" -print | xargs -P $(nproc --all) -I{} sh -c 'ffprobe -i "$1" -show_entries format=duration -v quiet -of csv="p=0" ' _ {} \; | (tqdm --total $(find -name "*.$srcext" | wc -l) ) | (awk '{ sum += $1 } END { print sum }')
```

**segmenting and resampling multiple audio files in parallel**

```bash
#**segmenting multiple audio files in parallel**
mkdir segments
srcext=wav; find . -type f -name "*.$srcext" -print | xargs -P $(nproc --all) -I{} sh -c 'mkdir -p "segments/$(dirname ${1})"; ffmpeg -i "$1" -hide_banner -y -loglevel panic -f segment -segment_time 6 -c copy "segments/${1%.*}.seg%03d.wav" && echo ""' -- {} | tqdm --total $(find -type f -name "*.$srcext" | wc -l) > /dev/null

# deletes source files
srcext=ogg; find . -type f -name "*.$srcext" -print | xargs -P $(nproc --all) -I{} sh -c 'ffmpeg -n -hide_banner -loglevel error -i "$1" -ar 22050 -ac 1 -f segment -segment_time 10 "${1%.*}.seg%03d.22050.wav" && rm "$1" | echo "" ' -- {} | tqdm --unit .$srcext --total $(find -type f -name "*.$srcext" | wc -l) > /dev/null
# doesn't delete source files
srcext=ogg; find . -type f -name "*.$srcext" -print | xargs -P $(nproc --all) -I{} sh -c 'ffmpeg -n -hide_banner -loglevel error -i "$1" -ar 22050 -ac 1 -f segment -segment_time 10 "${1%.*}.seg%03d.22050.wav" | echo "" ' -- {} | tqdm --unit .$srcext --total $(find -type f -name "*.$srcext" | wc -l) > /dev/null
```

**Delete corrupted files using `ffprobe`**

```bash
srcext=wav; 
find -type f -name "*.$srcext" -size  0 -print -delete
(find -type f -name "*.$srcext" -print  | xargs -P $(nproc --all) -I{} sh -c 'ffprobe -loglevel error -hide_banner "$1"; echo "$1,$?" ' -- {}) | xargs -I{} echo {} | (tqdm --total $(find -name "*.$srcext" | wc -l)) | grep ,1 | awk -F',' '{print $1}' | xargs -I{} rm "{}"
```

**For resetting timestamps of mp4 videos:**

```bash
# resetting timestamps of mp4 videos:
# $ ffmpeg -i "$1" -map 0 -y -vcodec copy "${1%.*}.sync.mp4"
find -name "*.mp4" -exec sh -c 'ffmpeg -i "$1" -map 0 -y -vcodec copy "${1%.*}.sync.mp4" &' sh {} \;
```

create empty librispeech-style transcripts  for audio datasets

```bash
# create empty librispeech-style transcripts  for audio datasets
find -type f -name "*.flac" -print | xargs -I{} sh -c 'echo "$(basename ${1%.*}) " >> "$(dirname "$1")/trans.trans.txt" ' -- {}
```

```bash
# deletl corrupted files
find . -name "*.gif" | tqdm | xargs -P $(nproc --all) -I{} sh -c 'ffmpeg -v error -i "{}" -f null - || rm "{}" 2>/dev/null'

```

## Using `rclone` for [box.com](http://box.com)

SpotifyPodcasts dataset specifically

```bash
$ curl https://rclone.org/install.sh | sudo bash
$ rclone config
- No remotes found - make a new one  n/s/q> n
- name> trecbox 
- Choose a number from below, or type in your own value Storage> 6
- client_id>:  #enter leave empty
- client_secret>: #enter leave empty
- box_config_file>: #enter leave empty
- box_sub_type>: 1
- Edit advanced config? (y/n)  y/n> n
- Use auto config? (y/n)  y/n> n
- Then paste the result below:
	- $ rclone authorize box #run this on a seperate terminal
	- result> {"access_token":"...","token_type":"bearer","refresh_token":"...","expiry":"2021-02-17T17:38:29.996461488+03:00"}

$ rclone ls trecbox:
$ rclone copy -P trecbox:Spotify-Podcasts-2020 /SpotifyPodcasts/ --max-backlog=999999 --drive-chunk-size=512M --transfers=45 --checkers=45 --buffer-size=75M
```
