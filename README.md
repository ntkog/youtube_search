# Prerequisites

You must have installed the following **cli** tools:
- [youtube-dl](https://rg3.github.io/youtube-dl/)
- [jq](https://stedolan.github.io/jq/)

# Installation

Just append the following to your **~/.bashrc**

```bash
function esri_search () {
   find $1 -type f -name '*.vtt.txt' -exec fgrep -H -B 1 -i "$2" {} \; | egrep 'txt-[0-9]{2}:' | sed -r 's/.txt/.txt|/' | cut -d'|' -f1 | sort | uniq -c | sort -nr
}
function getYoutubeVideosFromChannel () {
  youtube-dl -J --flat-playlist "$1" | jq '. | { channel : .uploader , videos : .entries | map({ url : "https://www.youtube.com/watch?v=\(.id)", title : .title})  }' > "$2"
}
function getCaptionsFromJson () {
  mkdir $3;
  cat "$1" | jq '.videos[] | .url' | sed -r 's/"//g' > list.tmp;
  youtube-dl -a list.tmp --skip-download --write-auto-sub --sub-lang $2 -o "./$3/%(upload_date)s/%(title)s.%(ext)s";
  rename_and_adapt;
}

function rename_and_adapt () {

  find . -type f -name '*.vtt' -print0 | xargs -0 rename 's/ /_/g';
  for i in `find . -name "*.vtt" -type f -print`; do echo ${i} ; tail -n +10 ${i} |  sed -r 's/(<c.color[a-f0-9]{6}>|<\/?c>|<[0-9]{2}\:[0-9]{2}\:[0-9]{2}.[0-9]{3}>)//gi' | sed -r 's/align\:start position\:19%//g' > ${i}.txt ; done
  find . -type f -name '*.vtt' -print0 | xargs -0 rename 's/.vtt$/.vtt.txt/';
}
```

Once you've done it, run the following in your terminal

```bash
source ~/.bashrc
```


# Usage

[![asciicast](https://asciinema.org/a/jKxntxqibxXFOUjb7oNPtceJq.png)](https://asciinema.org/a/jKxntxqibxXFOUjb7oNPtceJq)
