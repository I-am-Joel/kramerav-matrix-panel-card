# KramerAV Matrix Panel Card

<img width="1800" height="406" alt="The card displaying a Kramer VS-812 8 way switcher" src="https://github.com/user-attachments/assets/2898fd2f-47b4-4507-918a-464c645ef93d" />

A Home Assistant dashboard card that shows a Kramer matrix switcher as a virtual front panel and controls it the way the real unit works: choose an output, then choose an input.

It works with the [KramerAVhass integration](https://github.com/I-am-Joel/KramerAVhass) (version 0.14.2 or later). The card reads the integration's **Status** sensor and calls the integration's actions.

<img width="1818" height="1488" alt="The card displaying a Kramer VS-162AV matrix switcher" src="https://github.com/user-attachments/assets/985882ba-6103-433d-b8a5-7d986eb5d41e" />


## What it does

- One card per unit. The same layout serves every supported model, and buttons for features a model does not have are hidden.
- Routing: press an output (or **ALL**), then an input. The route is sent when the input is pressed.
- **OFF** disconnects the chosen output, on models that can do it. A disconnected output shows `--`.
- **VIDEO**, **AUDIO** and **AFV** show and change the layers, on models with separate audio and video switching.
- **STO** and **RCL** store and recall presets. On a unit with more presets than input buttons (for example 60 presets and 16 input buttons), the input buttons act as digits, and button 10 is 0. The window shows the prompt with a flashing colon, and the digits appear after it as you press them (`STORE: SELECT PRESET USING INPUT NUMBERS: 15`). Press two digits for a two-digit preset (**10** then **3** gives preset 3 straight away). If you press a single digit and stop, it is used as it stands after four seconds. A store needs **TAKE**; a filled preset needs **TAKE** to overwrite it. A recall needs no **TAKE**. Where every preset has its own input button, one press chooses it.
- A three-line status window shows what is happening, and seven segment displays show which input feeds each output.
- A straight (single-output) switch gets a simpler panel automatically: there is no OUTPUT section, no ALL button and no INPUT heading, and pressing an input routes it at once (`INPUT 05 > OUTPUT`). The lit input button shows the current route. Units with two or more outputs are unchanged.
- Six optional custom buttons run any Home Assistant action (a script, a scene or any action call). Each can be set to need **TAKE** first. To switch a device, choose **Perform action** and pick the action (for example `light.toggle`) and its target. **Toggle** and **More info** act on the optional **Entity** set for that button.
- **LOCK** locks that one display (held in that browser, surviving a reload), so a wall panel can be protected from stray touches. It does not touch the unit's own front panel lock. Hold the button for three seconds (you can change this in the editor) to lock or unlock. A normal tap only shows `HOLD FOR 3 SECONDS TO LOCK`, so an accidental touch cannot lock the panel. The window then shows `PANEL LOCKED` or `PANEL UNLOCKED`.
- Press and hold any button on a touch screen to see its long name in the status window.
- **Powered off.** With version 0.14.3 or later of the integration, a unit that is switched off still draws: all the lights go out, the seven segment displays go dark, the window shows `OFFLINE` and presses do nothing. The panel lights up again, with the current routes, when the unit answers. A card can also be added to a dashboard while the unit is off.

## Installation

1. In HACS, open the menu (three dots), choose **Custom repositories**, add `https://github.com/I-am-Joel/kramerav-matrix-panel-card` with the type **Dashboard**, and choose **Add**.
2. Find **KramerAV Matrix Panel Card** in HACS and choose **Download**.
3. Reload your browser (a hard refresh). HACS adds the card as a dashboard resource itself.
4. Edit a dashboard, choose **Add card**, and pick **Kramer Matrix Panel**.

## Setting it up

In the card editor, choose the unit's **Status** sensor (for example `sensor.dining_room_status`). Only the Status sensors of your Kramer switchers are listed. Everything else is optional. The editor has four tabs:

| Tab | What it holds |
| --- | --- |
| Configuration | The unit, buttons per row (2, 4, 8, 16 or 32), rack-mount ears, the status window, the seven segment displays and their colour, the function buttons (STO, RCL, LOCK, ALL, OFF and TAKE, which can be hidden for a simple switcher; TAKE stays if a custom button needs it), the LOCK button and its hold time, a switch for the custom buttons, the hold time before a button's long name is shown, and the model number text (with a switch that hides the text and the line above it, for a slightly shorter card). For a unit with a single output the seven segment options are not shown. |
| Button styles | The button colours for video, audio and audio follows video, with colour-blind-safe defaults and a "Return to default" button. |
| Custom buttons | The row heading, then six collapsible rows, each closed to start with. Each button has a label (up to four characters), a long name, an action, an optional entity (for Toggle and More info), and "Enforce PRESS "TAKE" before running". Every row has a Show switch (a hidden button stays in the list, greyed, and the buttons below it move up on the card), a handle to drag it to a new place (or Move up and Move down), and a two-step Delete that closes the gap. |
| Button names | Short (four-character) and long names for video inputs, audio inputs, video outputs and audio outputs (the output lists are not shown for a single-output unit). Short labels may be blank. Each list has a paste box (one name per line) and a "Use the integration's names" button. |

<img width="2054" height="1410" alt="Kramer matrix card editor view" src="https://github.com/user-attachments/assets/fb86f771-866f-45b7-b167-f05381fa8f0c" />

Names are held in the card and start from the integration's names, so each dashboard can label the same unit differently. A short label for a button whose integration name is still a default ("Input 001 Video" and the like) is left blank, and the button shows only its number.

<img width="1010" height="1096" alt="Define up to six of your own custom buttons" src="https://github.com/user-attachments/assets/d875dd4d-0fab-478b-b283-6bfb9c39120f" />

<img width="676" height="574" alt="kramer-matrix-card-custom-buttons" src="https://github.com/user-attachments/assets/7c71917e-d4c6-4337-98fb-43301f162aeb" />

### YAML

```yaml
type: custom:kramerav-matrix-panel-card
entity: sensor.dining_room_status
buttons_per_row: 8          # 2, 4, 8, 16 or 32 (automatic if left out)
rack_ears: true
lock_button: true
show_function: true         # false hides STO, RCL, LOCK, ALL, OFF and TAKE
show_custom_buttons: true   # false hides the custom buttons (their settings are kept)
show_window: true
show_segments: true
segment_color: "#ff2a24"
hold_time: 0.8              # press-and-hold for long names, seconds, 0.3 to 3
lock_hold_time: 3           # hold time for LOCK only, seconds, 1 to 10
model_text: ""              # blank shows the model name
show_model_text: true       # false hides the model text and the line above it
colors:
  video: "#56b4e9"
  audio: "#e69f00"
  together: "#009e73"
user_heading: USER
user_buttons:
  - label: FILM
    name: Film mode
    hidden: false             # true hides this button on the card
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

## Messages when the sensor cannot be used

| Message | What it means |
| --- | --- |
| Selected sensor not found. Check if device or integration is offline and that the sensor name is correct. | The sensor is missing, or it is `unavailable` with no layout (the integration is not loaded, or it is older than 0.14.3 and the unit is off). |
| Selected sensor is incorrect. A Kramer Status sensor must be selected. | The sensor exists but is not a Kramer Status sensor. |

## Limits

- The offline panel needs integration 0.14.3 or later. With an older integration a powered-off unit shows the first message above.
- One unit per card. Several units, daisy-chaining, other brands and the VP-729 scaler are not supported.
- The card does not show the unit's own front panel lock state.
- For custom buttons that call an action (`perform-action`), the card reports success or failure. Other action types (navigate, URL, more info, toggle) are handed to Home Assistant, which does not report back, so they always show as done.

## Accessibility

<img width="2050" height="1144" alt="Define the card's button colours" src="https://github.com/user-attachments/assets/85948c74-4f2a-4579-ae49-1fc09ad2fc90" />

- Every button can be reached with Tab and pressed with Enter or Space, and the focused button has a clear outline. To lock or unlock from the keyboard, hold Enter or Space on LOCK for the lock hold time.
- Each button announces its long name and state, for example "Output 3, Kitchen, selected". The status window is announced when its message changes.
- Nothing depends on colour alone, and all colours can be changed.
- With reduced motion requested, flashing becomes a steady light and scrolling stops. Nothing flashes more than three times a second.

## Licences

The card is released under the MIT licence (see `LICENSE`). The embedded fonts (Orbitron, Share Tech Mono and Barlow Semi Condensed) are under the SIL Open Font Licence 1.1 (see `FONT-LICENCES.md`).

This integration was not written by, or with the endorsement or support of, Kramer AV but serves the very real need of extending the life of models that are no longer supported by the manufacturer.
