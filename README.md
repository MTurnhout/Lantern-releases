# Lantern releases

Lantern is a small Windows companion for Claude Code. When a session finishes a longer piece of work,
is unsure about something, or needs you, a warm light appears in the corner of your screen with a short
note. Routine notes stay quiet; notes that need you come with a soft chime and a calm voice. **Open** on
a note takes you back to that session, and the arrows in its top-right corner make it bigger for reading
(Esc or the arrows again shrink it). Lantern remembers the size for the next notes.

This repository only holds the releases. Lantern updates itself from here if you turn on
"Update automatically" in its settings (tray: Settings...).

- [Install](#install)
- [Connect Claude Code](#connect-claude-code): the hooks, and `CLAUDE.md` so sessions send notes themselves
- [On a work or locked-down machine](#on-a-work-or-locked-down-machine)
- [Several machines, or moving the folder](#several-machines-or-moving-the-folder)
- [Settings](#settings)
- [Updates](#updates) · [Uninstall](#uninstall)

## Install

1. Download `Lantern-win-x64.zip` from the [latest release](../../releases/latest).
2. Extract it into a folder of its own that you can run programs from and write to, for example
   `C:\Dev\Lantern`. Not Program Files: Lantern keeps everything it writes in a `data\` folder next to
   the exe. No .NET install and no admin rights are needed.
3. In that folder, in PowerShell:

   ```powershell
   .\Lantern.exe install --autostart   # adds Lantern's hooks to Claude Code and starts it at sign-in
   .\Lantern.exe voice install         # optional: the neural voice (~340 MB)
   ```

Without `voice install` Lantern speaks with a Windows voice. Each release has a `.sha256` file next to
the zip to check the download.

## Connect Claude Code

### Hooks (automatic notes)

`install` adds three hooks to `~/.claude/settings.json` (UserPromptSubmit, Stop and Notification, each
running `"<this folder>\Lantern.exe" hook`). It keeps all your other settings and hooks and saves the
original once as `settings.json.bak-lantern`. With the hooks in place:

- a turn that took a while ends with a note made from the session's last message;
- a session waiting for a permission ends up as a *Needs you* note;
- short turns (under 30 seconds) stay quiet unless they end with a question;
- a session whose Claude Code closes in the middle of a turn leaves one quiet note, *Session ended
  while working*. It makes no sound. Turn it off in Settings.

Check it with `.\Lantern.exe status`, or send a test note: tray, **Settings...**, then **Try a
note**. The **Setup** part of Settings also says whether the hooks are installed, and can install
them.

### CLAUDE.md (notes sessions send themselves)

Sessions can also send a note on purpose, for example when a long task is done or when they are
blocked on you. Add this to `~/.claude/CLAUDE.md` (all projects) or to a project's `CLAUDE.md`, with the
path to your Lantern folder:

```markdown
# Lantern notes

`Lantern.exe` (at C:\Dev\Lantern\Lantern.exe) shows a short note on my desktop. Use it sparingly:

- When you finish a milestone of a long task, or the whole task, and I may not be watching:
  `Lantern.exe say "One-line headline." --done "what finished" --unsure "what you did not verify"`
- When you are blocked on me (a decision, a credential, a manual step), before you stop:
  `Lantern.exe say "Question in one line?" --kind question --need "what I must do"`
- Use `--kind error` when something failed and you cannot continue.
- Keep it short; only include the parts that apply. A body may use `- ` bullets, `**bold**` and
  `` `code` ``. Don't ping for trivial turns or routine progress: when you send a note, the end-of-turn
  hook stays quiet for that turn.
```

Lantern folder in a different place on each machine? Instead of a fixed path, write
"`Lantern.exe` is the command of the Lantern hooks in `~/.claude/settings.json`". Then one `CLAUDE.md`
works everywhere.

A note sent from inside a session belongs to that session: it gets **Open**, and it is cleared when
you reply in that session.

## On a work or locked-down machine

- **Where to put it.** Pick a folder your organisation lets you run programs from (for example
  `C:\Dev\Lantern`). Lantern never runs anything from `%TEMP%`, `%APPDATA%` or `%LOCALAPPDATA%`.
- **"Windows protected your PC".** Lantern is not code-signed yet. Either run
  `Unblock-File .\Lantern-win-x64.zip` before extracting, or choose *More info → Run anyway* once.
- **Start at sign-in blocked?** `install --autostart` says so and carries on, and so does *Start
  at sign-in* in Settings; start `Lantern.exe` yourself, or leave out `--autostart`.
- **The voice download fails** (huggingface.co blocked, or a proxy intercepting HTTPS). `voice install`
  then fetches the same files from this repository's `kokoro-v1.0` release. If GitHub is blocked
  too, copy the voice from a machine that has it:
  `.\Lantern.exe voice install --from \\other-pc\c$\Dev\Lantern` (every file is checked against its
  pinned SHA-256). Or skip the voice: Lantern then uses a Windows voice. Only download the model if
  your organisation's policy allows it.
- **Hooks don't run.** If your organisation's managed Claude Code settings only allow managed hooks
  (`allowManagedHooksOnly`, `disableAllHooks`), the hooks in `~/.claude/settings.json` are ignored.
  The [CLAUDE.md](#claudemd-notes-sessions-send-themselves) notes still work; ask whoever manages the
  settings if you want the automatic notes too.
- **Meetings.** In a call (an app is using the microphone), during a Focus session, or while you
  present or run something full screen, Lantern doesn't chime or speak. While you present or run
  something full screen, new notes only light the corner glow instead of opening the card. Settings:
  *Quiet during calls and presentations*. *Do not disturb* (tray or Settings) keeps the card closed
  and silences everything; *Quiet for an hour* does the same and ends by itself.

## Several machines, or moving the folder

- Each machine gets its own copy and its own `install`. Copy `data\config.json` along if you want the
  same settings.
- After moving the folder, run `.\Lantern.exe install` again from the new place. It repoints the
  hooks; running it twice changes nothing.
- The voice can be copied from another machine with `voice install --from <that Lantern folder>`.

## Settings

Everything Lantern keeps (settings, waiting notes, recent notes, logs, voice model) is in `data\` inside its folder.

The tray menu has the sessions, recent notes, the waiting notes, *Quiet for an hour*, *Do not disturb*
and **Settings...**. Under **Sessions**, each session has *Go to session* and *Mute until it ends*. A
muted session's notes still arrive and light the corner glow, but the card stays closed and nothing
sounds. Other sessions are not affected. The mute ends by itself when the session ends; click it
again to end it sooner.

**Recent** keeps the last 20 notes that went away: dismissed, cleared because you replied in the
session, expired, or replaced by a newer one. Choose one to read it again in the card. It opens even
under *Do not disturb*, and makes no sound. *Clear recent* empties the list. Notes older than a week
are dropped.

Settings opens a small window. Every change in it is saved at once:

- **Voice**: voice on or off, speak "done" notes, the chime, the engine (Auto, Kokoro or Windows),
  the speed, and **Try the voice**.
- **Quiet**: quiet during calls and presentations (see [Meetings](#on-a-work-or-locked-down-machine)),
  do not disturb, and quiet for an hour. That one ends by itself; while it is on, it shows when
  ("Quiet until 14:35"). A restart within the hour keeps it.
- **Placement**: the corner, and *Follow the monitor I'm using*. When on, a new note shows on the
  monitor you're using; when off, on the primary monitor.
- **Notes**: how long a turn must take before it makes a note, *Tell me when a session ends
  mid-turn*, *Larger card* (notes open in the bigger card; the arrows on the card switch it too), and
  **Try a note** (done, question or error).
- **Setup**: whether Claude Code's hooks are installed (with **Install**), start at sign-in, and
  whether the voice model is installed. The window doesn't download the voice; it shows the command.
- **Updates**: the version, **Check for updates** and **Update automatically**.
- **Open config file** and **Open logs folder**.

The rest is in `data\config.json`, which is applied as soon as you save it. The window shows your
edits too. The keys you're most likely to change:

| Key | Default | |
|---|---|---|
| `corner` | `"bottom-right"` | `"bottom-left"`, `"top-right"` or `"top-left"` |
| `monitor` | `"active"` | `"active"`: the monitor you're using when a note arrives; `"primary"`; a number (left to right); or a device name like `"\\\\.\\DISPLAY2"`. Config files from 0.1.2 and earlier have `"primary"` written in them: turn on *Follow the monitor I'm using* in Settings to get the new behaviour. |
| `voice.enabled` | `true` | speak notes that need you |
| `voice.engine` | `"auto"` | `"kokoro"` or `"winrt"` |
| `voice.rate` | `1.0` | speaking rate |
| `speakDone` | `false` | also speak routine notes |
| `chime` | `true` | the soft chime before notes that need you |
| `minTurnSeconds` | `30` | shorter turns make no note unless they end with a question |
| `sessionEndNotes` | `true` | a quiet note when a session's Claude Code closes mid-turn |
| `largeCard` | `false` | notes open in the bigger card (the arrows on the card set it too) |
| `quietWhenBusy` | `true` | see [Meetings](#on-a-work-or-locked-down-machine) |
| `doNotDisturb` | `false` | notes still arrive, but the card stays closed and nothing sounds |
| `updates.auto` | `false` | install updates automatically |

All of these are in Settings too.
## Updates

**Check for updates** in Settings installs the latest release in place, keeping `data\`. With
**Update automatically** on, Lantern does this by itself. You can also extract a new zip over the old
files after quitting Lantern.

Each release is signed, and Lantern checks the signature against a key built into it before it installs
an update. If the check fails, nothing is installed and Settings says "not signed by Lantern's key".

After an update, Lantern shows a "What's new" note once: "Lantern updated to x.y.z." with the main
changes from the release notes.

## Uninstall

```powershell
.\Lantern.exe uninstall --purge   # removes Lantern's hooks and autostart, and deletes data\
```

Then delete the folder. Your other hooks and the `settings.json.bak-lantern` backup stay.

---

Lantern is an independent project. It is not made, endorsed or supported by Anthropic.
