# ZMK Behaviors Cheat Sheet
## Behaviors

#### [Key Press Behaviors](https://zmk.dev/docs/behaviors/key-press)
```
&kp key
```
press the `key`.

#### [Momentary layer](https://zmk.dev/docs/behaviors/layers#momentary-layer)
```
&mo layer
```
momentary activate specified `layer`.

#### [To Layer](https://zmk.dev/docs/behaviors/layers#to-layer)
```
&to layer
```
Enable the `layer` and the default layer, and disable all other layers. (maybe it for &trans behavior).

#### [Toggle Layer](https://zmk.dev/docs/behaviors/layers#toggle-layer)
```
&tog layer
```
Enable the `layer` until the `layer` is disabled.

#### [Key Toggle Behavior](https://zmk.dev/docs/behaviors/key-toggle)
```
&kt key
```
toggle the `key` hold or released. 

#### [Sticky Key Behavior](https://zmk.dev/docs/behaviors/sticky-key)
```
&sk key
```
Stick the `key` till next key pressed.

#### [Sticky Layer Behavior](https://zmk.dev/docs/behaviors/sticky-layer)
```
&sl layer
```
Stick the `layer` till next key pressed.

#### [Transparent](https://zmk.dev/docs/behaviors/misc#transparent)
```
&trans
```
Pass down to the next active layer in the stack.

#### [None](https://zmk.dev/docs/behaviors/misc#none)
```
&none
```
No behavior.

#### [Caps Word Behavior](https://zmk.dev/docs/behaviors/caps-word)
```
&caps_word
```
Capitalize till stop key pressed. stop key is not alphabet and `continue-list`.

#### [Key Repeat Behavior](https://zmk.dev/docs/behaviors/key-repeat)
```
&key_repeat
```
Repeats the last key.

## [Conditional Layers](https://zmk.dev/docs/features/conditional-layers)
```
    conditional_layers {
        compatible = "zmk,conditional-layers";
        tri_layer {
            if-layers = <1 2>;
            then-layer = <3>;
        };
    };
```
If some multiple layers are enabled (in above example, `1` and `2`), the then-layer (`3`) will be enabled.

## Hold-Tap related

#### [Hold-Tap Behavior](https://zmk.dev/docs/behaviors/hold-tap)
Advanced Mod-Tap and Layer-Tap.

![](https://zmk.dev/assets/images/case1_2-0c88b39aeb0de9b5f5651c8274fa83f3.svg)

released within `tapping-term-ms`, sends tap key. hold after the time, sends (holds) the hold key.
```
    behaviors {
        key: name {
            compatible = "zmk,behavior-hold-tap";
            label = "LABEL";
            #binding-cells = <2>;
            tapping-term-ms = <200>;
            quick-tap-ms = <0>;
            flavor = "flavor";
            bindings = <hold>, <tap>;
        };
    };
```
`flavor` in (`hold-preferred`, `balanced`, `tap-preferred`, `tap-unless-interrupted`)
`<hold>`, `<tap>` is behavior like `<&kp>`, `<&mo>`.
![](https://zmk.dev/assets/images/comparison-90bcec61f679e88a74490eb261d92940.svg)

#### [Mod-Tap Behavior](https://zmk.dev/docs/behaviors/mod-tap)
```
&mt modkey tapkey
```
hold to send `modkey`, tap to send `tapkey`.

This behavior equals the Hold-tap "hold-preferred" with (`hold` = key press(`&kp`), `tap` = key press(`&kp`)).
```
    behaviors {
        mt_impl: mod-tap-by-hold-tap {
            compatible = "zmk,behavior-hold-tap";
            label = "MOD_TAP_BY_HOLD_TAP";
            #binding-cells = <2>;
            tapping-term-ms = <200>;
            quick-tap-ms = <0>;
            flavor = "hold-preferred";
            bindings = <&kp>, <&kp>;
        };
    };

    // &mt modkey tapkey = &mt_impl modkey tapkey
```

#### [Layer-tap](https://zmk.dev/docs/behaviors/layers#layer-tap)
```
&lt layer key
```
tap sends the `key`, holds activate the `layer`.

This behavior equals the Hold-tap "tap-preferred" with (`hold` = momentary layer(`&mo`), `tap` = key press(`&kp`)).
```
    behaviors {
        lt_impl: layer-tap-by-hold-tap {
            compatible = "zmk,behavior-hold-tap";
            label = "LAYER_TAP_BY_HOLD_TAP";
            #binding-cells = <2>;
            tapping-term-ms = <200>;
            quick-tap-ms = <0>;
            flavor = "tap-preferred";
            bindings = <&mo>, <&kp>;
        };
    };

    // &lt layer key = &lt_impl layer key
```

## Other definitions

#### [Mod-Morph Behavior](https://zmk.dev/docs/behaviors/mod-morph)
```
    behaviors {
        key: name {
            compatible = "zmk,behavior-mod-morph";
            label = "LABEL";
            #binding-cells = <0>;
            bindings = <without mod behavior>, <with mod behavior>;
            mods = <(MOD_LGUI|MOD_LSFT|MOD_RGUI|MOD_RSFT)>;
            keep-mods = <(MOD_LSFT)>;
        };
    };
```
with MOD_* sends `with mod behavior`, without MOD_* sends the `without mod behavior`.

Available mods: `MOD_LSFT`, `MOD_RSFT`, `MOD_LCTL`, `MOD_RCTL`, `MOD_LALT`, `MOD_RALT`, `MOD_LGUI`, `MOD_RGUI`.

keep-mods' `MOD_*` will be keep holding with behavior.

#### [Tap-Dance Behavior](https://zmk.dev/docs/behaviors/tap-dance)
```
    behaviors {
        td0: tap_dance_0 {
            compatible = "zmk,behavior-tap-dance";
            label = "TAP_DANCE_0";
            #binding-cells = <0>;
            tapping-term-ms = <200>;
            bindings = <&kp N1>, <&kp N2>, <&kp N3>;
        };
    };
```
Change behavior by the count of tap.

## [Macro Behavior](https://zmk.dev/docs/behaviors/macros)
```
    macros {
        zed_em_kay: zed_em_kay {
            label = "ZM_zed_em_kay";
            compatible = "zmk,behavior-macro";
            #binding-cells = <0>;
            bindings
                = <&macro_press &kp LSHFT>
                , <&macro_tap &kp Z &kp M &kp K>
                , <&macro_release &kp LSHFT>
                ;
        };
    };
```
WIP.

## Hardware or firmware related

#### [Reset Behaviors](https://zmk.dev/docs/behaviors/reset)
```
&reset
```
Reset with current flashed firmware. (like restart)

#### [Bootloader Reset](https://zmk.dev/docs/behaviors/reset#bootloader-reset)
```
&bootloader
```
Reset to flash new firmware.

#### [Bluetooth Behavior](https://zmk.dev/docs/behaviors/bluetooth)
```
&bt BT_*
```
- BT_CLR : clear current connection info
- BT_NXT : switch to the next profile
- BT_PRV : switch to the previous profile
- BT_SEL number : switch to the 0-index number profile

#### [Output Selection Behavior](https://zmk.dev/docs/behaviors/outputs)
```
&out OUT_*
```
- OUT_USB : prefer use usb
- OUT_BLE : prefer use bluetooth low energy 
- OUT_TOG : toggle above

#### [RGB Underglow Behavior](https://zmk.dev/docs/behaviors/underglow)
```
&rgb_ug RGB_*
```
WIP

#### [Backlight Behavior](https://zmk.dev/docs/behaviors/backlight)
```
&bl BL_*
```
WIP

#### [Power Management Behaviors](https://zmk.dev/docs/behaviors/power)
```
&ext_power EP_*
```
WIP
