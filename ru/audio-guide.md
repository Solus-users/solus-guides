## Включение Pipewire для лучшей обработки звука с минимальными задержками

```bash
sudo eopkg it `pipewire`, `wireplumber` && systemctl --user enable pipewire && systemctl --user enable --now wireplumber && systemctl --user disable pulseaudio pulseaudio.socket && systemctl --user enable pipewire-pulse.socket pipewire-pulse && reboot
```

**Этот гайд на других языках**:

English: https://github.com/Solus-users/solus-guides/blob/main/en/audio-guide.md
