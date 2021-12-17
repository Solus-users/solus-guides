# Pipewire instead other server for handling audio

## Enable Pipewire

```bash
sudo eopkg it wireplumber
systemctl --user enable pipewire
systemctl --user enable --now wireplumber
systemctl --user disable pulseaudio pulseaudio.socket
systemctl --user enable pipewire-pulse.socket pipewire-pulse
reboot
```


**Other languages**:

Русский: https://github.com/Solus-users/solus-guides/blob/main/ru/audio-guide.md
