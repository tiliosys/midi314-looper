
midi@3:14 is a home-made electronic keyboard for playing music.

This repository contains an application that can record audio loops and that
responds to MIDI control messages from midi@3:14.

See [midi314-pcb](https://github.com/tiliosys/midi314-pcb) and
[midi314-firmware](https://github.com/tiliosys/midi314-firmware)
for more information about the keyboard itself.

Building
========

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
=======

From the root of the source tree, start the looper:

```
./target/release/midi314-looper
```

In another terminal, start FluidSynth:

```
fluidsynth
```

In a third terminal, list the available input and output ports:

```
pw-link -i
pw-link -o
```

Connect the keyboard to FluidSynth (replace "141542" with the actual port number):

```
pw-link "Midi-Bridge:Arduino Leonardo:(capture_0) Arduino Leonardo MIDI 1" "Midi-Bridge:FLUID Synth (141542):(playback_0) Synth input port (141542:0)"
```

Connect the looper with the keyboard, FluidSynth, and the audio output (some port names may differ on your system):

```
pw-link "Midi-Bridge:Arduino Leonardo:(capture_0) Arduino Leonardo MIDI 1" midi314-looper:midi_in
pw-link "PipeWire ALSA [fluidsynth]:output_FL" midi314-looper:audio_in_1
pw-link "PipeWire ALSA [fluidsynth]:output_FR" midi314-looper:audio_in_2
pw-link midi314-looper:audio_out_1 alsa_output.pci-0000_00_1b.0.analog-stereo:playback_FL
pw-link midi314-looper:audio_out_2 alsa_output.pci-0000_00_1b.0.analog-stereo:playback_FR
```
