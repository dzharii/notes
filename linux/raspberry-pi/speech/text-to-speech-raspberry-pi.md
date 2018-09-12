## Text to Speech on Raspberry Pi

Source: https://elinux.org/RPi_Text_to_Speech_(Speech_Synthesis)

Pico Text to Speech
Google Android TTS engine. Very good quality speech.

```
sudo apt-get install aplay
sudo apt-get install libttspico-utils
pico2wave -w lookdave.wav "Look Dave, I can see you're really upset about this." && aplay lookdave.wav

```