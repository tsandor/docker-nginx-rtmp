# docker-nginx-rtmp
A Dockerfile installing NGINX, nginx-rtmp-module and FFmpeg from source with
default settings for HLS live streaming. Built on Alpine Linux.

* Nginx 1.16.0 (Stable version compiled from source)
* nginx-rtmp-module 1.2.1 (compiled from source)
* ffmpeg 4.2 (compiled from source)
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
docker run -it --log-driver=fluentd --log-opt tag='docker.nginx.{{.ID}}' --log-opt fluentd-address=`docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' fluentd_web_1`:24224 -p 1935:1935 -p 8080:80 -v /tmp/rec:/srv/rec --rm nginx-rtmp
```

mpv http://localhost:8080/hls/hello.m3u8

curl http://localhost:8080/dash/hello_720p2628kbs/index.mpd

/tmp/dash/hello_720p2628kbs

Start fluentd 
```
cd fluentd
docker build -t custom-fluentd:latest ./
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' fluentd_web_1
docker-compose up --build

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

### SSL 
To enable SSL, see [nginx.conf](nginx.conf) and uncomment the lines:
```
listen 443 ssl;
ssl_certificate     /opt/certs/example.com.crt;
ssl_certificate_key /opt/certs/example.com.key;
```

This will enable HTTPS using a self-signed certificate supplied in [/certs](/certs). If you wish to use HTTPS, it is **highly recommended** to obtain your own certificates and update the `ssl_certificate` and `ssl_certificate_key` paths.

I recommend using [Certbot](https://certbot.eff.org/docs/install.html) from [Let's Encrypt](https://letsencrypt.org).

### OBS Configuration
* Stream Type: `Custom Streaming Server`
* URL: `rtmp://localhost:1935/stream`
* Stream Key: `hello`

### Watch Stream
* In Safari, VLC or any HLS player, open:
```
http://<server ip>:8080/live/$STREAM_NAME.m3u8
```
* Example Playlist: `http://localhost:8080/live/hello.m3u8`
* [VideoJS Player](https://video-dev.github.io/hls.js/stable/demo/?src=http%3A%2F%2Flocalhost%3A8080%2Flive%2Fhello.m3u8)
* FFplay: `ffplay -fflags nobuffer rtmp://localhost:1935/stream/hello`

### FFmpeg Build
```
$ ffmpeg -buildconf

ffmpeg version 4.2 Copyright (c) 2000-2019 the FFmpeg developers
  built with gcc 6.4.0 (Alpine 6.4.0)
  configuration: --prefix=/usr/local --enable-version3 --enable-gpl --enable-nonfree --enable-small --enable-libmp3lame --enable-libx264 --enable-libx265 --enable-libvpx --enable-libtheora --enable-libvorbis --enable-libopus --enable-libfdk-aac --enable-libass --enable-libwebp --enable-librtmp --enable-postproc --enable-avresample --enable-libfreetype --enable-openssl --disable-debug --disable-doc --disable-ffplay --extra-libs='-lpthread -lm'
  libavutil      56. 31.100 / 56. 31.100
  libavcodec     58. 54.100 / 58. 54.100
  libavformat    58. 29.100 / 58. 29.100
  libavdevice    58.  8.100 / 58.  8.100
  libavfilter     7. 57.100 /  7. 57.100
  libavresample   4.  0.  0 /  4.  0.  0
  libswscale      5.  5.100 /  5.  5.100
  libswresample   3.  5.100 /  3.  5.100
  libpostproc    55.  5.100 / 55.  5.100

  configuration:
    --prefix=/usr/local
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
    --disable-doc
    --disable-ffplay
    --extra-libs='-lpthread -lm'
```

## Resources
* https://alpinelinux.org/
* http://nginx.org
* https://github.com/arut/nginx-rtmp-module
* https://www.ffmpeg.org
* https://obsproject.com
