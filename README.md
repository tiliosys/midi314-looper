
midi@3:14 is a home-made electronic keyboard for playing music.

This repository contains an application that can record audio loops and that
responds to MIDI control messages from midi@3:14.

See [midi314-pcb](https://github.com/tiliosys/midi314-pcb) and
[midi314-firmware](https://github.com/tiliosys/midi314-firmware)
for more information about the keyboard itself.

MIDI events
-----------

This program supports the following custom MIDI Control-Change events:

| Identifier | Event                  |
|:-----------|:-----------------------|
| 20         | Start recording a loop |
| 21         | Play a loop            |
| 22         | Mute a loop            |
| 23         | Delete a loop          |
| 24         | Mute all other loops   |
| 25         | Unmute all muted loops |

Building
--------

This crate uses [rust-jack](https://github.com/RustAudio/rust-jack).
It requires that you install Jack or Pipewire Jack using your package manager.
On Fedora, I use:

```
sudo dnf install pipewire-jack-audio-connection-kit-devel
```

Build the program using this command from the root of the source tree:

```
cargo build --release
```

Running
-------

From the root of the source tree, start the looper:

```
./target/release/midi314-looper
```

In another terminal, start FluidSynth:

```
fluidsynth -p FluidSynth
```

In a third terminal, connect the keyboard to FluidSynth:

```
pw-link "Midi-Bridge:Arduino Leonardo:(capture_0) Arduino Leonardo MIDI 1" "Midi-Bridge:FluidSynth:(playback_0) FluidSynth"
```

Connect the MIDI input of the looper with the keyboard:

```
pw-link "Midi-Bridge:Arduino Leonardo:(capture_0) Arduino Leonardo MIDI 1" midi314-looper:midi_in
```

Connect the audio input of the looper to FluidSynth:

```
pw-link "PipeWire ALSA [fluidsynth]:output_FL" midi314-looper:audio_in_1
pw-link "PipeWire ALSA [fluidsynth]:output_FR" midi314-looper:audio_in_2
```

Connect the output of the looper to your system audio output.
You can use `pw-link -i` to get the actual port names for your system:

```
pw-link midi314-looper:audio_out_1 alsa_output.pci-0000_00_1b.0.analog-stereo:playback_FL
pw-link midi314-looper:audio_out_2 alsa_output.pci-0000_00_1b.0.analog-stereo:playback_FR
```
