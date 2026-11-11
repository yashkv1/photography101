# Photography 101

A step-by-step course for learning what a camera lens does. Type a lens (for example `18-200mm f/4-6.3`) or pick one from the built-in library, then work through the modules:

| # | Module | You practise |
|---|--------|--------------|
| 1 | Focal length | Zooming, angle of view, "half as wide" |
| 2 | Sensor and crop factor | Equivalent focal length on different sensors |
| 3 | Aperture and stops | f-numbers, halving the light |
| 4 | Variable aperture | How the widest aperture shrinks as you zoom |
| 5 | Depth of field | Thin focus vs everything sharp |
| 6 | Background blur | Zoom, aperture and distance |
| 7 | Shutter speed and shake | Handheld rule, stabilization, freezing motion |
| 8 | Exposure triangle | Aperture + shutter + ISO with a light meter |
| ★ | Playground | Every control at once |

Each module has a short explanation, only the controls it needs, practice tasks that tick off automatically (in order) and a check-yourself question. Tasks adapt to whatever lens is loaded, and ones that can't apply (like zoom tasks on a prime) are skipped. Progress is saved in the browser. Each module has its own link, such as `index.html#depth-of-field`.

## Running it

It's a single file with no build step and no network requests. Open `index.html` in any modern browser.

To host it with GitHub Pages: push this repo, then go to **Settings → Pages**, pick the branch and the root folder, and save.

## How it's built

- [Preact](https://preactjs.com/) plus [htm](https://github.com/developit/htm) (MIT licensed). The `htm/preact/standalone` build (v3.1.1) is pasted into the first `<script>` tag, so the page works offline.
- All app code is in the second `<script>` tag:
  - `RAW`: the lens library (name, category, format, stabilization, and exact aperture steps where published)
  - `SENSORS`: sensor sizes and crop factors
  - `parseLens()`: reads text like `18-200mm f/3.5-6.3`
  - `maxApAt()`: widest aperture at a given focal length (estimated between the two ends unless exact steps are listed)
  - `dof()`: depth of field using the standard hyperfocal formula (circle of confusion = sensor diagonal ÷ 1442)
  - `compute()`: every derived number (equivalent focal length, depth of field, blur, exposure error) from the current settings
  - `MODULES`: the course. Each module has `intro`, `idea`, `controls`, `visuals`, `tasks` and `quiz`
  - `App`: the shell (lens bar, module list, progress)

## Adding a module

Add an object to `MODULES`:

```js
{
  id: 'my-topic', title: 'My topic',
  intro: 'One paragraph explaining the idea.',
  idea: 'The one-line takeaway.',
  controls: ['zoom', 'aperture'],          // any of: sensor, light, zoom, aperture, focus, is, shutter, iso
  visuals: S => html`<${PreviewView} S=${S}/>`,
  tasks: S => [{id: 'a', text: 'Do X.', ok: S.f === S.f1, na: S.f0 === S.f1}],
  quiz: {q: 'Question?', opts: ['A', 'B', 'C'], ans: 1, why: 'Explanation.'},
}
```

`S` holds the current state from `compute()`. `ok` marks a task done; `na` skips it for lenses where it can't apply.

## Adding a lens

Add a row to `RAW`. The focal range and aperture are read from the name:

```js
['Brand 18-200mm F3.5-6.3 Model','super','APS-C',1],
// category: super | std | tele | prime | wide
// format:   FF | APS-C | MFT
// 1 = has optical stabilization
// optional 5th item: exact aperture steps, e.g. [[150,5],[242,5.6],[390,6.3],[500,6.7]]
```

## Accuracy

Lens specs come from manufacturer pages and reviews. Apertures between the two ends of a zoom are estimates unless the lens lists exact steps. Depth of field and blur are guides, not guarantees.
