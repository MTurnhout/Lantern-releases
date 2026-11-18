# Lantern releases

Lantern is a small Windows companion for Claude Code. When a session finishes a longer piece of work,
is unsure about something, or needs you, a warm light appears in the corner of your screen with a short
note. Routine notes stay quiet; notes that need you come with a soft chime and a calm voice.

This repository only holds the releases. Lantern updates itself from here if you turn on
"Update automatically" in its tray menu.

## Install

1. Download `Lantern-win-x64.zip` from the [latest release](../../releases/latest) and extract it into a
   folder you can run programs from, for example `C:\Dev\Lantern`. No .NET install is needed.
2. In that folder run `.\Lantern.exe install --autostart`. This adds Lantern's hooks to
   `~/.claude/settings.json` (after a backup) and starts it with Windows.
3. For the neural voice run `.\Lantern.exe voice install` (about 340 MB), or copy the model from
   another machine with `.\Lantern.exe voice install --from <folder>`. Without it Lantern uses a
   Windows voice.

Everything Lantern stores lives in the `data\` folder next to the exe.
To remove it: `.\Lantern.exe uninstall --purge`, then delete the folder.

Each release has a `.sha256` file next to the zip to check the download.

---

Lantern is an independent project. It is not made, endorsed or supported by Anthropic.
