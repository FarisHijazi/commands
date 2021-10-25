# handy commandline

## Killing processes

```bash
## kill last process (probably used after ctrl-z)
kill $(jobs -l 2>&1|cut -d' ' -f2|head -n1)

## kill grepped processes from `ps aux`
# this example kills any process with `ffmpeg` in its launch command
pgrep -x jupyter|xargs kill -9 
## longer way:
ps aux | grep jupyter | awk '{print $2}' | xargs sudo kill -9

## delete every other file, but save the first one
find . -name "filenames...*" |head -n -1 |awk "NR%2==1 {print}" |xargs -I{} rm {}
```

## Hashing a directory

```bash
# solution provied by this answer: https://stackoverflow.com/a/545413/7771202

# hash folder (without permissions)
rm sha1sum_without_permissions; find . -type f -print0 | sort -z | xargs -P $(nproc --all) -0 sha1sum | awk '{ print $1 }' | tqdm --unit file --total $(find . -type f | wc -l) | sha1sum | tee sha1sum_without_permissions

# to hash permissions
rm sha1sum_permissions; (find . -type f -print0  | sort -z | xargs -P $(nproc --all) -0 sha1sum | awk '{ print $1 }'; 
find . \( -type f -o -type d \) -print0 | sort -z | xargs -P $(nproc --all) -0 stat -c '%n %a') | tqdm --unit file --total $(find . -type f | wc -l) | sha1sum | awk '{ print $1 }'| tee sha1sum_permissions
```

## Autostart an app before login

```bash
# auto start app before login
echo '[Unit]
Description=Barrier mouse/keyboard share
Requires=display-manager.service
After=display-manager.service
StartLimitIntervalSec=0

[Service]
Type=forking
ExecStart=/anp/bin/barrier --no-restart --name e-jetsonnano #192.168.10.12
Restart=always
RestartSec=10
User=pi

[Install]
WantedBy=multi-user.target' | sudo tee /etc/systemd/system/barrier.service
sudo chmod +x /etc/systemd/system/barrier.service
```

## Double quotes vs single quotes in shell

[Check this table](https://stackoverflow.com/a/42082956)

[three-point formula](https://stackoverflow.com/questions/10067266/when-to-wrap-quotes-around-a-shell-variable/42104627#42104627)

## parallel download URLs from file

```bash
## parallel download URLs from file

mkdir downloads
cd downloads
cat ../urls.txt|xargs -n 1 -I{} -P $(nproc --all) sh -c 'wget -nc -qq "{}" && echo ""' |tqdm --total $(cat ../urls.txt|wc -l) --unit file >/dev/null
rename 's/%0D$//' *
rename 's/$/.gif/'  $(find -not -name '*.gif' -not -name '*.jpg' -not -name '*.png' -not -name '*.gifv')
find -size 1 -delete
find -size 0 -delete
ls * |xargs -I{} -P $(nproc --all) sh -c 'identify "{}"> /dev/null || rm "{}"'
```

## installing and using nvidia Docker

pop!os dependency issue:

[https://github.com/pop-os/pop/issues/1708#issuecomment-855337760](https://github.com/pop-os/pop/issues/1708#issuecomment-855337760)

```bash
# for pop!os 20.04, you must increase pin priority for nvidia channels using th following command
echo 'Package: *
Pin: origin nvidia.github.io
Pin-Priority: 1002' | sudo tee -a /etc/apt/preferences.d/pop-default-settings

distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
   && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
   && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y nvidia-docker2
sudo systemctl restart docker

## using it
sudo docker build docker/ --rm -t projectname

# use -v to mount any folders you want for the filesystem
sudo nvidia-docker run --shm-size=1g --ulimit memlock=-1 --ulimit stack=67108864 -it --rm --ipc=host \
		-v $PWD:/workspace/projectname/ -v /data:/data --name workspace projectname bash

cd /workspace/projectname/
python ....

# test
# sudo docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
```

## Random stuff

### Torrents

```bash
# on Windows
pip install we-get
choco install -y qbittorrent
(we-get -L -s 'search term') | xargs -I{} '/c/Program Files/qBittorrent/qbittorrent.exe' {}
```

then get the downloaded json file and run this command (be sure to change `ddg_json`

```bash
rawurlencode() {
  #https://stackoverflow.com/a/10660730/7771202
  local string="${1}"
  local strlen=${#string}
  local encoded=""
  local pos c o

  for (( pos=0 ; pos<strlen ; pos++ )); do
     c=${string:$pos:1}
     case "$c" in
        [-_.~a-zA-Z0-9] ) o="${c}" ;;
        * )               printf -v o '%%%02x' "'$c"
     esac
     encoded+="${o}"
  done
  echo "${encoded}"    # You can either set a return variable (FASTER) 
  REPLY="${encoded}"   #+or echo the result (EASIER)... or both... :p
}

# using wget
ddg_json=duckduckgo.com.json; 
mkdir "${ddg_json%.*}_dl"; 
cat "$ddg_json" |jq '.[] | .image +" "+ .title' -c | sed -e 's/^.//' -e 's/.$//' | while read -r url name ;do (wget -nc -O "${ddg_json%.*}_dl/${name}.$(url="${url%\?*}" && echo ${url##*.})" "https://external-content.duckduckgo.com/iu/?u=$( rawurlencode $url)&f=1" & ) && echo '' ; done; 
find "${ddg_json%.*}_dl" -type f -size 0 -delete
# $(url="${url%\?*}" && echo ${url##*.})
# this expression is just to get the filextension from the URL without the leading "?"

## using curl (can encode URI components and get DDG proxy links (unblocks images)
ddg_json=duckduckgo.com.json; mkdir "${ddg_json%.*}_dl";
list=$(cat "$ddg_json" |jq -e '.[] | .image +";"+ .title');
cd "${ddg_json%.*}_dl";
echo $list | sed -e 's/^.//' -e 's/.$//' |  tr '\n' ';'|tr ';' '\0' |xargs -P 10 -n2  -0 sh -c 'wget -nc -O "${2}.$(url="${1%\?*}" && echo ${url##*.})" "https://external-content.duckduckgo.com/iu/?u=$( rawurlencode $url)&f=1" | echo "" ' sh | tqdm --unit image --total $(echo $list |wc -l) > /dev/null

find "${ddg_json%.*}_dl" -type f -size 0 -delete
# $(url="${url%\?*}" && echo ${url##*.})

```

```jsx
cat 'RARBG info.json'  | jq '.torrents[] | .magnetLink'  | xargs /c/Program\ Files/qBittorrent/qbittorrent.exe
```

