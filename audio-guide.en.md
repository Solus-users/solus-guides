# Pipewire instead other server for handling audio

## How to enable Pipewire

1.Enable (without having to run under sudo) the user PipeWire service and socket with:
```bash 
systemctl --user enable pipewire
```

2. When continue with the user PipeWire PulseAudio: 
```bash 
systemctl --user enable pipewire-pulse
```

3. And the PipeWire Multimedia Service Session Manager: 
```bash 
systemctl --user enable pipewire-media-session
```

4. Disable the PulseAudio socket and service with: 
```bash 
systemctl --user disable pulseaudio pulseaudio.socket
```

5. Install the pipewire-jack package if you want to test JACK support: 
```bash 
sudo eopkg it pipewire-jack
```

6. Reboot

# PulseAudio config

```bash
sudo micro /usr/share/pulseaudio/daemon.conf
```

* daemonize = no
* high-priority = yes
* nice-level = -11
* realtime-scheduling = yes
* realtime-priority = 9
* resample-method = soxr-vhq
* enable-remixing = no
* rlimit-rtprio = 9
* default-sample-format = float32le
* default-sample-rate = 48000 \\\ you can set more if the audio card allows, i'm using 192000
* alternate-sample-rate = 44100 \\\ you can set more if the audio card allows, i'm using 96000
* default-sample-channels = 2
* default-channel-map = front-left,front-right
* default-fragments = 2
* default-fragment-size-msec = 125
