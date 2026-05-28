# godot-piper-plus

`godot-piper-plus` teeth Godot 4.4 Offline speech synthesis, available from now on. addon is
[`piper-plus`](https://github.com/mahshid1378/piper-plus) of Godot for GDExtension provided as、`PiperTTS` node and editor You can embed local TTS using the tool.

For those who want to try it right away:

- public demo: [https://mahshid1378.github.io/godot-piper-plus/](https://mahshid1378.github.io/godot-piper-plus/)
- package README: [`addons/piper_plus/README.md`](./addons/piper_plus/README.md)
- API Reference: [`doc_classes/PiperTTS.xml`](./doc_classes/PiperTTS.xml)

## What you can do

- Locally-based neural speech synthesis
- `ja/en/zh/es/fr/pt` の explicit text input / inspect / synthesize
- `ja/en` auto-routing and multilingual capability confirmation
- same period / asynchronous / streaming synthesis
- `inspect_*` API by dry-run / timing obtain
- model downloader、dictionary editor、Inspector expansion、test speech UI、By language template text
- `execution_provider` and `gpu_device_id` by backend switching

## Things to know before using

- audio model teeth package Not included in
- Japanese text input for `naist-jdic` is required
- downloader The default save location is `res://piper_plus_assets/models/` and `res://piper_plus_assets/dictionaries/` is
- asset after placing runtime It runs locally.
- child repository as is Godot project When opening as demo scene teeth `res://demo/main.tscn` is

## Procedure introduction

1. Godot 4.4 subsequent project to `addons/piper_plus` Place.
2. `Project > Project Settings > Plugins` in **Piper Plus TTS** Enable it.
3. `Piper Plus: Download Models...` Open and check at least one model Add.
4. When using Japanese synthesis `naist-jdic` also add.
5. scene to `PiperTTS` or add script from `PiperTTS.new()` It will be generated using this method.

Try it in English as quickly as possible model This is the easiest. English version `cmudict_data.json` teeth addon It is included on the side.

## Minimal code example

The following example is `AudioStreamPlayer` of 1 have one scene This is based on the premise that...
`model_path` and `config_path` teeth downloader This is an example using the default placement.

```gdscript
extends Node

@onready var player: AudioStreamPlayer = $AudioStreamPlayer

func _ready() -> void:
    var tts := PiperTTS.new()
    add_child(tts)

    tts.model_path = "res://piper_plus_assets/models/en_US-ljspeech-medium/en_US-ljspeech-medium.onnx"
    tts.config_path = "res://piper_plus_assets/models/en_US-ljspeech-medium/en_US-ljspeech-medium.onnx.json"

    tts.synthesis_completed.connect(func(audio: AudioStreamWAV) -> void:
        player.stream = audio
        player.play()
    )
    tts.synthesis_failed.connect(func(message: String) -> void:
        push_error(message)
    )

    var err := tts.initialize()
    if err != OK:
        push_error("PiperTTS initialize failed: %s" % err)
        return

    err = tts.synthesize_async("Hello from Piper Plus.")
    if err != OK:
        push_error("PiperTTS synthesize_async failed: %s" % err)
```

- model If you manually place it,`model_path` and `config_path` Please adjust this to match the actual placement location.
- If you use Japanese,`dictionary_path` to `res://piper_plus_assets/dictionaries/open_jtalk_dic_utf_8-1.11` Please set.

## models and dictionaries

model The main body is package It is not included.
`res://piper_plus_assets/models/` You can manually place it,editor of downloader This assumes that the data will be obtained from [source].

Easy-to-choose configuration:

| use | Recommended asset | supplement |
|---|---|---|
| Try English right away | `en_US-ljspeech-medium` | The easiest configuration to start with |
| Try Japanese | `ja_JP-test-medium` | `naist-jdic` is required |
| I want to improve the sound quality of Japanese. | `tsukuyomi-chan` | `naist-jdic` is required |
| Web demo / smoke Try under the same conditions. | `multilingual-test-medium` | Used in public demo |
| Japanese text input dictionary | `naist-jdic` | Required in Japanese |

Current language support:

- `ja` / `en`: preview tier。auto / explicit of text input support
- `zh` / `es` / `fr` / `pt`: experimental explicit-only。`language_code` or `language_id` Assuming explicit specification text input support
- Windows / Web of repo The implementation uses 6 languages selector / template text / smoke of shared catalog and descriptor foundation We have adopted a configuration that uses these elements.

A more precise authentic version is [`tests/fixtures/multilingual_capability_matrix.json`](./tests/fixtures/multilingual_capability_matrix.json) and [`tests/fixtures/multilingual_sample_text_catalog.json`](./tests/fixtures/multilingual_sample_text_catalog.json) is. runtime descriptor foundation teeth [`addons/piper_plus/model_descriptors/multilingual-test-medium.json`](./addons/piper_plus/model_descriptors/multilingual-test-medium.json) That's it. The materials that people read are [`docs/generated/multilingual_capability_matrix.md`](./docs/generated/multilingual_capability_matrix.md) と [`docs/generated/multilingual_sample_text_catalog.md`](./docs/generated/multilingual_sample_text_catalog.md) Please refer to

## Support status

This is a guideline for current users. For detailed verification history and the latest official version, please see [link/website]. [`docs/milestones.md`](./docs/milestones.md) Please refer to

| platform | situation | supplement |
|---|---|---|
| Windows | Confirmed | source build and packaged addon smoke confirmed |
| Linux | Confirmed | CI build and headless integration continues to run |
| macOS | Confirmed | packaged addon smoke of CI Confirmed with |
| Android | in progress | export smoke That has been confirmed. The rest are runtime Possibility and Windows local export difference |
| iOS | Confirmed | export / link smoke of CI Confirmed with |
| Web export | preview support | browser smoke teeth `no-threads` preset in canonical 6-language synthesize gate、`Web Threads` preset in non-blocking Na English/core regression smoke This configuration involves rotating the following.repo Well then 6-language selector / template text / Japanese dictionary staging / local-public smoke loop It has been implemented up to this point, and the rest is workflow and public deploy This is confirmed. custom template and `EP_CPU` premise |

## GitHub Pages public demo

The public demo GitHub Pages It's available now.

- URL: [https://ayutaz.github.io/godot-piper-plus/](https://ayutaz.github.io/godot-piper-plus/)
- Current publication URL  `main` to deploy Already done artifact will deliver
- repo side Pages demo The implementation is canonical 6-language selector / template text、shared descriptor foundation、staged `naist-jdic`、`ja/en/zh/es/fr/pt` of local / public smoke loop It has been expanded to this extent.
- Included model: `multilingual-test-medium`
- Japanese text input teeth staged `naist-jdic` use

The public demo addon This is an entrance to quickly get a feel for the atmosphere. addon its own Web export Not yet preview support in, public URL of live scope is the latest deploy Follow the results.

## Web export

addon its own Web export teeth preview support is.

- custom Web export template is required
- toolchain teeth Godot 4.4.1 towards `emsdk 3.1.62` This is based on the premise that
- `execution_provider` teeth `EP_CPU` It is fixed
- `openjtalk-native` shared library teeth Web It cannot be used in
- self-hosting If you do `COOP` / `COEP` with static server or equivalent cross-origin isolation workaround is required
- local browser smoke teeth `GODOT=/path/to/godot bash scripts/ci/export-web-smoke.shteeth build/web-smoke` This can be reproduced. By default, `Web` preset but `ja/en/zh/es/fr/pt` of synthesize gate、`Web Threads` preset but `en` of non-blocking regression smoke Execute. Node.js and Playwright is required
- Pages demo of local smoke teeth `node scripts/ci/run-pages-demo-smoke.mjs --root <site_dir> --scenario <language_code>` This can be reproduced. scenario teeth sample text catalog of 6 same as language

The operational notes for the public demo are [`docs/web-github-pages-plan.md`](./docs/web-github-pages-plan.md) Please refer to

## Editor Tools

addon is the following editor command We provide.

- `Piper Plus: Download Models...`
- `Piper Plus: Dictionary Editor...`
- `Piper Plus: Test Speech...`

`PiperTTS` In the node custom Inspector enters, preset Application, download link, dictionary editing, preview UI of Inspector Open it from here.

## main API

The APIs you'll most often use first are as follows:

- `initialize()`
- `synthesize(text)` / `synthesize_async(text)`
- `synthesize_streaming(text, playback)`
- `inspect_text(text)` / `inspect_request(request)`
- `get_last_error()` / `get_language_capabilities()`

For detailed API information, see [`doc_classes/PiperTTS.xml`](./doc_classes/PiperTTS.xml) Please refer to

## package Included / Not Included

What's included:

- `addons/piper_plus` The GDExtension and editor plugins under it
- for English `cmudict_data.json`
- Web preview use `web.*` entry

What is not included:

- audio model (`.onnx` / `.onnx.json`)
- for Japanese `naist-jdic`
- `openjtalk-native` Main body

## Known limitations

- Android has been confirmed to work up to CI export smoke, but the final check for runtime compatibility remains.
- Troubleshooting generic configuration errors remains for Android headless export from Windows local.
- Web export is in preview support and is not officially supported.
- multilingual auto-routing teeth `ja/en` That's the main focus. `zh/es/fr/pt` This is an experimental tier that assumes explicit selection.
- The Windows packaged addon for 6 languages, Smoke, and Web/Pages workflow testing is ongoing.

## Detailed information

- For package users: [`addons/piper_plus/README.md`](./addons/piper_plus/README.md)
- API: [`doc_classes/PiperTTS.xml`](./doc_classes/PiperTTS.xml)
- Progress and support status: [`docs/milestones.md`](./docs/milestones.md)
- Ticket list: [`docs/tickets/README.md`](./docs/tickets/README.md)
- Pages Public Notes: [`docs/web-github-pages-plan.md`](./docs/web-github-pages-plan.md)
- runtime descriptor: [`addons/piper_plus/model_descriptors/multilingual-test-medium.json`](./addons/piper_plus/model_descriptors/multilingual-test-medium.json)
- language contract: [`docs/generated/multilingual_capability_matrix.md`](./docs/generated/multilingual_capability_matrix.md) and [`docs/generated/multilingual_sample_text_catalog.md`](./docs/generated/multilingual_sample_text_catalog.md)
- CI / CD: [`.github/workflows/build.yml`](./.github/workflows/build.yml) and [`.github/workflows/pages.yml`](./.github/workflows/pages.yml)

## Related projects

- [piper-plus](https://github.com/ayutaz/piper-plus)
- [uPiper](https://github.com/ayutaz/uPiper)
- [dot-net-g2p](https://github.com/ayutaz/dot-net-g2p)

## license

[Apache License 2.0](./LICENSE)
