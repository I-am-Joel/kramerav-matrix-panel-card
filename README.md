# KramerAV Matrix Panel Card

A Home Assistant dashboard card that shows a Kramer matrix switcher as a virtual front panel and controls it the way the real unit works: choose an output, then choose an input.

It works with the [KramerAVhass integration](https://github.com/I-am-Joel/KramerAVhass) (version 0.14.2 or later). The card reads the integration's **Status** sensor and calls the integration's actions. It never talks to a unit directly, and it never contacts an external server: the fonts it uses are inside the card file.

> **Not endorsed.** This card is not written by, or endorsed by, Kramer AV. It carries no Kramer logo or branding. The panel is a tribute to the look of industrial rack equipment.

## What it does

- One card per unit. The same layout serves every supported model, and buttons for features a model does not have are hidden.
- Routing: press an output (or **ALL**), then an input. The route is sent when the input is pressed.
- **OFF** disconnects the chosen output, on models that can do it. A disconnected output shows `--`.
- **VIDEO**, **AUDIO** and **AFV** show and change the layers, on models with separate audio and video switching.
- **STO** and **RCL** store and recall presets. On a unit with more presets than input buttons (for example 60 presets and 16 input buttons), the input buttons act as digits, and button 10 is 0. The window shows the prompt with a flashing colon, and the digits appear after it as you press them (`STORE: SELECT PRESET USING INPUT NUMBERS: 15`). Press two digits for a two-digit preset (**10** then **3** gives preset 3 straight away). If you press a single digit and stop, it is used as it stands after four seconds. A store needs **TAKE**; a filled preset needs **TAKE** to overwrite it. A recall needs no **TAKE**. Where every preset has its own input button, one press chooses it.
- A three-line status window shows what is happening, and seven segment displays show which input feeds each output.
- A straight (single-output) switch gets a simpler panel automatically: there is no OUTPUT section, no ALL button and no INPUT heading, and pressing an input routes it at once (`INPUT 05 > OUTPUT`). The lit input button shows the current route. Units with two or more outputs are unchanged.
- Six optional user buttons run any Home Assistant action (a script, a scene or any action call). Each can be set to need **TAKE** first. To switch a device, choose **Perform action** and pick the action (for example `light.toggle`) and its target. **Toggle** and **More info** act on the optional **Entity** set for that button.
- **LOCK** locks that one display (held in that browser, surviving a reload), so a wall panel can be protected from stray touches. It does not touch the unit's own front panel lock. Hold the button for three seconds (you can change this in the editor) to lock or unlock. A normal tap only shows `HOLD FOR 3 SECONDS TO LOCK`, so an accidental touch cannot lock the panel. The window then shows `PANEL LOCKED` or `PANEL UNLOCKED`.
- Press and hold any button on a touch screen to see its long name in the status window.

## Installation

1. In HACS, open the menu (three dots), choose **Custom repositories**, add `https://github.com/I-am-Joel/kramerav-matrix-panel-card` with the type **Dashboard**, and choose **Add**.
2. Find **KramerAV Matrix Panel Card** in HACS and choose **Download**.
3. Reload your browser (a hard refresh). HACS adds the card as a dashboard resource itself.
4. Edit a dashboard, choose **Add card**, and pick **Kramer Matrix Panel**.

## Setting it up

In the card editor, choose the unit's **Status** sensor (for example `sensor.dining_room_status`). Only the Status sensors of your Kramer switchers are listed. Everything else is optional. The editor has four tabs:

| Tab | What it holds |
| --- | --- |
| Configuration | The unit, buttons per row (2, 4, 8, 16 or 32), rack-mount ears, the LOCK button, the status window, the seven segment displays and their colour, the function buttons (STO, RCL, LOCK, ALL, OFF and TAKE, which can be hidden for a simple switcher; TAKE stays if a user button needs it), the lock hold time, the press-and-hold time for long names, and the text on the bottom line. |
| Button styles | The glow colours for video, audio and together (audio follows video), with colour-blind-safe defaults and a "Return to default" button. |
| User-defined buttons | The row heading, and for each of six buttons a label (up to four characters), a long name, an action, an optional entity (for Toggle and More info), and "Enforce PRESS "TAKE" before running". |
| Button names | Short (four-character) and long names for video inputs, audio inputs, video outputs and audio outputs. Short labels may be blank. Each list has a paste box (one name per line) and a "Use the integration's names" button. |

Names are held in the card and start from the integration's names, so each dashboard can label the same unit differently. A short label for a button whose integration name is still a default ("Input 001 Video" and the like) is left blank, and the button shows only its number.

### YAML

```yaml
type: custom:kramerav-matrix-panel-card
entity: sensor.dining_room_status
buttons_per_row: 8          # 2, 4, 8, 16 or 32 (automatic if left out)
rack_ears: true
lock_button: true
show_function: true         # false hides STO, RCL, LOCK, ALL, OFF and TAKE
show_window: true
show_segments: true
segment_color: "#ff2a24"
hold_time: 0.8              # press-and-hold for long names, seconds, 0.3 to 3
lock_hold_time: 3           # hold time for LOCK only, seconds, 1 to 10
model_text: ""              # blank shows the model name
colors:
  video: "#56b4e9"
  audio: "#e69f00"
  together: "#009e73"
user_heading: USER
user_buttons:
  - label: FILM
    name: Film mode
    enforce_take: true
    entity: light.hall        # optional: for Toggle and More info
    action:
      action: perform-action
      perform_action: script.film_mode
names:
  video_outputs:
    - short: LNGE
      long: Lounge
```

## Limits

- One unit per card. Several units, daisy-chaining, other brands and the VP-729 scaler are not supported.
- The card does not show the unit's own front panel lock state.
- For user buttons that call an action (`perform-action`), the card reports success or failure. Other action types (navigate, URL, more info, toggle) are handed to Home Assistant, which does not report back, so they always show as done.

## Accessibility

- Every button can be reached with Tab and pressed with Enter or Space, and the focused button has a clear outline. To lock or unlock from the keyboard, hold Enter or Space on LOCK for the lock hold time.
- Each button announces its long name and state, for example "Output 3, Kitchen, selected". The status window is announced when its message changes.
- Nothing depends on colour alone, and all colours can be changed.
- With reduced motion requested, flashing becomes a steady light and scrolling stops. Nothing flashes more than three times a second.
- When buttons may be too small to press reliably on a touch screen, the editor says so.

## Licences

The card is released under the MIT licence (see `LICENSE`). The embedded fonts (Orbitron, Share Tech Mono and Barlow Semi Condensed) are under the SIL Open Font Licence 1.1 (see `FONT-LICENCES.md`).
