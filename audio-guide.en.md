# Pipewire instead other server for handling audio

## How to enable

1. Enable (without having to run under sudo) the user PipeWire service and socket with: 
```bash 
systemctl --user enable pipewire```

2. When continue with the user PipeWire PulseAudio: 
```bash 
systemctl --user enable pipewire-pulse```

3. And the PipeWire Multimedia Service Session Manager: 
```bash 
systemctl --user enable pipewire-media-session```

4. Disable the PulseAudio socket and service with: 
```bash 
systemctl --user disable pulseaudio pulseaudio.socket```

5. Install the pipewire-jack package if you want to test JACK support: 
```bash 
sudo eopkg it pipewire-jack```

6. Reboot
