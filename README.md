[中文文档](README_zh.md)

# Gemini Watermark Remover — Free Browser-Based Gemini Watermark Removal Tool

A fast, privacy-friendly **Gemini AI watermark remover** that runs entirely in your browser. Instead of relying on uncertain AI inpainting, it uses pure JavaScript and a mathematically grounded **Reverse Alpha Blending** workflow to restore watermark-affected pixels with consistent results.

> **Try our online tool:** [ducttape3.org/en/tools/gemini-watermark-remover](https://ducttape3.org/en/tools/gemini-watermark-remover) — tired of Gemini watermarks? Open the free web version to upload, preview, and download cleaned images instantly. For new image generation workflows, you can also discover **GPT-Image-2** resources through our site.

<p align="center">
  <a href="https://ducttape3.org/en/tools/gemini-watermark-remover"><img src="https://img.shields.io/badge/Online_Tool-ducttape3.org-6366f1?style=for-the-badge" alt="Online Gemini Watermark Remover"></a>&nbsp;
  <a href="https://github.com/cf12436/gemini-watermark-remover"><img src="https://img.shields.io/badge/GitHub-Source_Code-181717?style=for-the-badge" alt="GitHub Source Code"></a>
</p>

## Why Use the Online Tool

- **No installation** — open the page and process Gemini images directly in your browser.
- **Privacy-friendly** — image processing runs locally in the browser whenever supported.
- **Fast preview workflow** — upload, compare the before/after result, then copy or download the processed image.
- **Free access** — the hosted tool is available at [ducttape3.org/en/tools/gemini-watermark-remover](https://ducttape3.org/en/tools/gemini-watermark-remover).

## Features

- ✅ **100% Local Processing** - All image processing happens locally in your browser or on your machine. Nothing is uploaded by the core workflow.
- ✅ **Mathematical Precision** - Based on the Reverse Alpha Blending formula, not "hallucinating" AI models.
- ✅ **Auto-Detection** - Automatically identifies watermark size and position using Gemini's known output catalog and local anchor search.
- ✅ **Flexible Usage** - Online tool for quick use, Chrome extension or userscript for seamless Gemini page integration, CLI and Skill for scripting and automation.
- ✅ **Cross-Platform** - Works in modern browsers (Chrome, Firefox, Safari, Edge) and Node.js environments.

## Gemini Watermark Removal Examples

<details open>
<summary>Click to Expand/Collapse Examples</summary>

| Original Image | Watermark Removed |
| :---: | :----: |
| <img src="docs/demo-before.webp" width="400"> | <img src="docs/demo-after.webp" width="400"> |

</details>

## ⚠️ Disclaimer

> [!WARNING]
>  **USE AT YOUR OWN RISK**
>
> This tool modifies image files. While it is designed to work reliably, unexpected results may occur due to:
> - Variations in Gemini's watermark implementation
> - Corrupted or unusual image formats
> - Edge cases not covered by testing
>
> The author assumes no responsibility for any data loss, image corruption, or unintended modifications. By using this tool, you acknowledge that you understand these risks.

> [!NOTE]
> **Note**: Disable any fingerprint defender extensions (e.g., Canvas Fingerprint Defender) to avoid processing errors. https://github.com/GargantuaX/gemini-watermark-remover/issues/3

## How to Remove Gemini Watermarks

### Online Tool (Recommended)

The fastest and easiest way to remove a Gemini watermark is the hosted browser tool:

1. Open **[ducttape3.org/en/tools/gemini-watermark-remover](https://ducttape3.org/en/tools/gemini-watermark-remover)**.
2. Drop, paste, or click to upload your Gemini-generated image.
3. Let the tool detect the visible bottom-right Gemini watermark and restore the affected pixels locally.
4. Compare before and after with the preview slider.
5. Copy or download the processed result.

> Bookmark the official online tool: [https://ducttape3.org/en/tools/gemini-watermark-remover](https://ducttape3.org/en/tools/gemini-watermark-remover)

### CLI

For scripting, CI, and local batch workflows:

```bash
# repo-local
node bin/gwr.mjs remove <input> --output <file>

# installed globally
gwr remove <input> [--output <file> | --out-dir <dir>] [--overwrite] [--json]
```

### Userscript

1. Install a userscript manager (e.g., Tampermonkey or Greasemonkey).
2. Build the userscript with `pnpm build`.
3. Install from `dist/userscript/gemini-watermark-remover.user.js`.
4. Navigate to Gemini conversation pages — eligible images are processed automatically.

## Development

```bash
# Install dependencies
pnpm install

# Development build
pnpm dev

# Production build
pnpm build

# Local static serving
pnpm serve
```

`pnpm dev` starts a local dev server (default port `4173`, auto-increments if occupied). The root path `/` serves the main watermark removal tool.

## SDK Usage (Advanced / Internal)

The package root still exposes an SDK, but this path is intended for advanced or internal integration scenarios:

```javascript
import {
  createWatermarkEngine,
  removeWatermarkFromImage,
  removeWatermarkFromImageData,
  removeWatermarkFromImageDataSync,
} from '@pilio/gemini-watermark-remover';
```

Use the pure-data API when you already have decoded `ImageData`:

```javascript
const result = await removeWatermarkFromImageData(imageData, {
  adaptiveMode: 'auto',
  maxPasses: 4,
});

console.log(result.meta.decisionTier);
```

Use the browser image API when you have an `HTMLImageElement` or `HTMLCanvasElement`:

```javascript
const { canvas, meta } = await removeWatermarkFromImage(imageElement);
document.body.append(canvas);
console.log(meta.applied, meta.decisionTier);
```

If you need to process many images, reuse a single engine instance so alpha maps stay cached:

```javascript
const engine = await createWatermarkEngine();
const first = await removeWatermarkFromImageData(imageDataA, { engine });
const second = await removeWatermarkFromImageData(imageDataB, { engine });
```

For Node.js integrations, use the dedicated subpath and inject your own decoder/encoder:

```javascript
import { removeWatermarkFromBuffer } from '@pilio/gemini-watermark-remover/node';

const result = await removeWatermarkFromBuffer(inputBuffer, {
  mimeType: 'image/png',
  decodeImageData: yourDecodeFn,
  encodeImageData: yourEncodeFn,
});
```

## Runtime Requirements

### Web And Userscript

- modern Chrome / Firefox / Safari / Edge class browser
- ES modules
- Canvas API
- Async/Await
- TypedArray (`Float32Array`, `Uint8ClampedArray`)
- for the website copy button: `navigator.clipboard.write(...)` and `ClipboardItem`

### CLI And Skill

- a local Node.js runtime capable of running this package and its dependencies
- filesystem access for local input/output paths
- for repo-local usage:

```bash
node bin/gwr.mjs remove <input> --output <file>
node skills/gemini-watermark-remover/scripts/run.mjs remove <input> --output <file>
```

- for distributed Skill usage, the local environment must be able to execute the packaged `gwr` CLI boundary

## Testing

```bash
# Run all tests
pnpm test
```

Regression tests include image fixtures from `src/assets/samples/`.
Source samples stay in git.
Naming and retention rules for those fixtures are documented in `src/assets/samples/README.md`.
Complex preview/download validation notes are documented in `docs/complex-figure-verification-checklist.md`.
Local files under `src/assets/samples/fix/` are optional snapshot outputs for manual regression checks and are intentionally not tracked by git.

## Release Notes

See [CHANGELOG.md](CHANGELOG.md) for release history and [RELEASE.md](RELEASE.md) for the local release checklist.

## How Gemini Watermark Removal Works

### The Gemini Watermarking Process

Gemini applies watermarks using standard alpha compositing:

$$watermarked = \alpha \cdot logo + (1 - \alpha) \cdot original$$

Where:
- `watermarked`: The pixel value with the watermark.
- `α`: The Alpha channel value (0.0 - 1.0).
- `logo`: The watermark logo color value (White = 255).
- `original`: The raw, original pixel value we want to recover.

### The Reverse Solution

To remove the watermark, we solve for `original`:

$$original = \frac{watermarked - \alpha \cdot logo}{1 - \alpha}$$

By capturing the watermark on a known solid background, we reconstruct the exact Alpha map and apply the inverse formula to restore the original pixels with zero loss.

## Detection Rules

The engine uses layered detection to locate and verify watermarks:

1. **Size catalog lookup** — matches image dimensions against Gemini's known output sizes to predict watermark size and position.
2. **Local anchor search** — refines the predicted position by scanning pixel data around the expected watermark region.
3. **Restoration validation** — confirms the detected watermark is real before applying removal, avoiding false positives.

Default watermark configurations:

| Condition | Watermark Size | Right Margin | Bottom Margin |
| :--- | :--- | :--- | :--- |
| Larger Gemini outputs | 96×96 | 64px | 64px |
| Smaller Gemini outputs | 48×48 | 32px | 32px |

## Project Structure

```text
gemini-watermark-remover/
├── bin/                   # Published CLI entrypoint (`gwr`)
├── public/
│   ├── index.html         # Main watermark removal tool page
│   ├── dev-preview.html   # Legacy internal preview harness
│   └── tampermonkey-worker-probe.*  # Probe pages for userscript/debug flows
├── skills/
│   └── gemini-watermark-remover/    # Distributable agent skill bundle
├── src/
│   ├── assets/            # Calibration assets and regression samples
│   ├── cli/               # CLI argument parsing and file workflows
│   ├── core/              # Watermark math, scoring, and restoration
│   ├── page/              # Page-side runtime for Gemini page integration
│   ├── sdk/               # Advanced/internal SDK surface
│   ├── shared/            # Shared DOM, blob, and session helpers
│   ├── userscript/        # Userscript entrypoints and browser hooks
│   ├── workers/           # Worker runtime
│   ├── app.js             # Main web app entry point
│   └── utils.js           # Shared browser helpers
├── tests/                 # Unit, regression, packaging, and smoke tests
├── scripts/               # Local automation and debug launchers
├── dist/                  # Build output directory
├── build.js               # Build script
└── package.json
```

## Architecture Overview

- `src/core/` contains watermark detection, candidate selection, restoration metrics, and the reverse-alpha removal pipeline.
- `src/userscript/`, `src/page/`, and `src/shared/` implement the real Gemini page integration, including preview replacement plus copy/download interception.
- `src/cli/` and `bin/gwr.mjs` expose file-oriented local automation.
- `skills/gemini-watermark-remover/` provides a distributable Skill that stays on the CLI boundary instead of importing repository internals directly.
- `src/sdk/` remains available for advanced/internal integrations, but it is no longer the primary public entrypoint.

---

## Limitations

- Only removes **Gemini visible watermarks** <small>(the semi-transparent logo in bottom-right)</small>
- Does not remove invisible/steganographic watermarks. <small>[(Learn more about SynthID)](https://support.google.com/gemini/answer/16722517)</small>
- Designed for Gemini's current visible watermark pattern <small>(validated against this repo through April 2026)</small>

## Legal Disclaimer

This project is released under the **MIT License**.

The removal of watermarks may have legal implications depending on your jurisdiction and the intended use of the images. Users are solely responsible for ensuring their use of this tool complies with applicable laws, terms of service, and intellectual property rights.

The author does not condone or encourage the misuse of this tool for copyright infringement, misrepresentation, or any other unlawful purposes.

**THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY CLAIM, DAMAGES, OR OTHER LIABILITY ARISING FROM THE USE OF THIS SOFTWARE.**

## Credits

This project is a JavaScript port of the [Gemini Watermark Tool](https://github.com/allenk/GeminiWatermarkTool) by Allen Kuo ([@allenk](https://github.com/allenk)).

The Reverse Alpha Blending method and calibrated watermark masks are based on the original work © 2024 AllenK (Kwyshell), licensed under MIT License.

## Related Links

- [Online Gemini Watermark Remover on ducttape3.org](https://ducttape3.org/en/tools/gemini-watermark-remover) — Recommended hosted version of this tool
- [ducttape3.org](https://ducttape3.org) — More AI tools and resources
- [Gemini Watermark Tool](https://github.com/allenk/GeminiWatermarkTool) — Original Python implementation
- [Removing Gemini AI Watermarks: A Deep Dive into Reverse Alpha Blending](https://allenkuo.medium.com/removing-gemini-ai-watermarks-a-deep-dive-into-reverse-alpha-blending-bbbd83af2a3f) — Algorithm deep dive

## License

[MIT License](./LICENSE)
