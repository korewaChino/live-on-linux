# Ableton Live on Linux with Wine

[Ableton Live] is a DAW (Digital Audio Workstation) developed by Ableton and is available for macOS and Windows. It is not officially supported on Linux, but it can be run using [Wine].

This repository provides written instructions on how to install and run Ableton Live on Linux using Wine, and a simple wrapper script to integrate the app as a menu entry and MIME handler.

[wine]: https://www.winehq.org/
[ableton live]: https://www.ableton.com/en/live/

## Installation

You will need to install Wine (of course) and Winetricks to install the necessary runtime dependencies. You can install them using your package manager. Then you should set up a Wine prefix for Ableton Live, preferrably using the main Wine prefix (usually `~/.wine`).

```bash
WINEPREFIX=~/.wine wineboot
```

Change `WINEPREFIX` to the path of your Wine prefix if it is not `~/.wine`. You do not need to set `WINEPREFIX` if you're using `~/.wine` anyway.

Now, install Visual C++ 2015 and 2017 runtimes using Winetricks.

```bash
WINEPREFIX=~/.wine winetricks vcrun2015 vcrun2017
```

Download the latest version of Ableton Live from the [official website](https://www.ableton.com/en/live/). The installation process is straightforward and does not require any special instructions.

After installing Ableton Live using the system Wine installation, install the wrapper script to your $PATH (e.g. `~/.local/bin`) and make it executable.

```bash
cp ./abletonlive ~/.local/bin
chmod +x ~/.local/bin/abletonlive
```

Then run the wrapper script to create the necessary menu entry and MIME handler.

```bash
abletonlive --create-desktop-entry
```

You may now run Ableton Live from the menu or by running `abletonlive` in the terminal.
You can also activate the Ableton Live license like you would on Windows or macOS using online activation, as the wrapper script also handles the `ableton:` URI scheme
used by the license activation page.

> [!NOTE]
> After the first run, you may need to reset Max for Live's preferences to avoid a crash on startup. You can do this by deleting `<prefix>/drive_c/users/<user>/AppData/Roaming/Cycling '74/Max 8/Settings/maxpreferences.maxpref`. All subsequent runs should work without any issues.

## WineASIO

### Installing on Ultramarine/Fedora-based systems

WineASIO is a virtual ASIO driver that allows ASIO-compatible applications to use JACK as their audio backend. This is useful for running Ableton Live with JACK, as it does not support JACK natively.

On Ultramarine or Fedora w/ Terra, you can install WineASIO with PipeWire support from Terra.

```bash
sudo dnf install wineasio
```

### Installing on Debian

On Debian-based systems, you can install WineASIO from the [KXStudio repositories](https://kx.studio/Repositories).

### Building from source

To build WineASIO from source, you will need to install the necessary build dependencies. You can install them using your package manager.

```bash
# Install build dependencies on Fedora
sudo dnf install pipewire-jack-audio-connection-kit-devel wine-devel
```

```bash
# Clone the repo
cd /tmp
git clone https://github.com/wineasio/wineasio
cd wineasio
git submodule update --init --recursive

# Compile
make 64
```

### Installing

After installing (or building) WineASIO, all you need to do is run `wineasio-register` on the desired Wine prefix.

```bash
WINEPREFIX=~/.wine wineasio-register
```

This should copy the necessary drivers to the Wine prefix and register them.

## Known issues

- When using PipeWire with WineASIO, you may get occasional audio artifacts caused by XRuns. This is a known issue with PipeWire and is being worked on, you may try changing the buffer size in the PipeWire settings to mitigate this issue, and consider setting up real-time scheduling on your system.
