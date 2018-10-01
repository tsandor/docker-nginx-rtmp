# docker-nginx-rtmp
A Dockerfile installing NGINX, nginx-rtmp-module and FFmpeg from source with
default settings for HLS live streaming. Built on Alpine Linux.

* Nginx 1.13.9 (compiled from source)
* nginx-rtmp-module 1.2.1 (compiled from source)
* ffmpeg 3.4.2 (compiled from source)
* Default HLS settings (See: [nginx.conf](nginx.conf))

[![Docker Stars](https://img.shields.io/docker/stars/alfg/nginx-rtmp.svg)](https://hub.docker.com/r/alfg/nginx-rtmp/)
[![Docker Pulls](https://img.shields.io/docker/pulls/alfg/nginx-rtmp.svg)](https://hub.docker.com/r/alfg/nginx-rtmp/)
[![Docker Automated build](https://img.shields.io/docker/automated/alfg/nginx-rtmp.svg)](https://hub.docker.com/r/alfg/nginx-rtmp/builds/)
[![Build Status](https://travis-ci.org/alfg/docker-nginx-rtmp.svg?branch=master)](https://travis-ci.org/alfg/docker-nginx-rtmp)

## Usage

### Server
* Pull docker image and run:
```
docker pull alfg/nginx-rtmp
docker run -it -p 1935:1935 -p 8080:80 --rm alfg/nginx-rtmp
```
or 

* Build and run container from source:
```
docker build -t nginx-rtmp .
docker run -it -p 1935:1935 -p 8080:80 --rm nginx-rtmp
```

* Run it with fluentd log driver:
```
mkdir -p /tmp/rec
docker run -it --log-driver=fluentd --log-opt tag='docker.nginx.{{.ID}}' --log-opt fluentd-address=172.17.0.2:24224 -p 1935:1935 -p 8080:80 -v /tmp/rec:/srv/rec --rm --privileged nginx-rtmp
```

```
docker build -t nginx-rtmp . 
docker run -it --log-driver=fluentd --log-opt tag='docker.nginx.{{.ID}}' --log-opt fluentd-address=172.17.0.2:24224 -p 1935:1935 -p 8080:80 -v /tmp/rec:/srv/rec --rm nginx-rtmp
```

curl http://localhost:8080/dash/hello_720p2628kbs/index.mpd
/tmp/dash/hello_720p2628kbs

```
docker build -t custom-fluentd:latest ./
docker run -it --rm --name custom-docker-fluent-logger -v $(pwd)/log:/fluentd/log custom-fluentd:latest
```

* Number of clients
```
Use HTTP request http://localhost/nclients?app=myapp&name=mystream to get the number of stream subscribers. This number will be automatically refreshed every 3 seconds when opened in browser or iframe.
```

* Stream live content to:
```
rtmp://<server ip>:1935/stream/$STREAM_NAME
```

### OBS Configuration
* Stream Type: `Custom Streaming Server`
* URL: `rtmp://localhost:1935/stream`
* Stream Key: `hello`

### Watch Stream
* In Safari, VLC or any HLS player, open:
```
http://<server ip>:8080/live/$STREAM_NAME.m3u8
```
* Example: `http://localhost:8080/live/hello`


### FFmpeg Build
```
ffmpeg version 3.4.2 Copyright (c) 2000-2018 the FFmpeg developers
  built with gcc 5.3.0 (Alpine 5.3.0)
  configuration: --enable-version3 --enable-gpl --enable-nonfree --enable-small --enable-libmp3lame --enable-libx264 --enable-libx265 --enable-libvpx --enable-libtheora --enable-libvorbis --enable-libopus --enable-libfdk-aac --enable-libass --enable-libwebp --enable-librtmp --enable-postproc --enable-avresample --enable-libfreetype --enable-openssl --disable-debug
  libavutil      55. 78.100 / 55. 78.100
  libavcodec     57.107.100 / 57.107.100
  libavformat    57. 83.100 / 57. 83.100
  libavdevice    57. 10.100 / 57. 10.100
  libavfilter     6.107.100 /  6.107.100
  libavresample   3.  7.  0 /  3.  7.  0
  libswscale      4.  8.100 /  4.  8.100
  libswresample   2.  9.100 /  2.  9.100
  libpostproc    54.  7.100 / 54.  7.100

  configuration:
    --enable-version3
    --enable-gpl
    --enable-nonfree
    --enable-small
    --enable-libmp3lame
    --enable-libx264
    --enable-libx265
    --enable-libvpx
    --enable-libtheora
    --enable-libvorbis
    --enable-libopus
    --enable-libfdk-aac
    --enable-libass
    --enable-libwebp
    --enable-librtmp
    --enable-postproc
    --enable-avresample
    --enable-libfreetype
    --enable-openssl
    --disable-debug
```

## Resources
* https://alpinelinux.org/
* http://nginx.org
* https://github.com/arut/nginx-rtmp-module
* https://www.ffmpeg.org
* https://obsproject.com
