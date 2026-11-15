# Checking your work

Procedures for verifying UI before anyone else sees it. Every check runs headless or off-screen and leaves the person's own environment alone: their dev server, their database, their browser profile and their screen. If the project documents its own scripts or switches for this, use those instead of the generic recipes here.

## Contents

1. Ground rules
2. Widths and overflow
3. Browser engines
4. A stale dev server
5. Desktop app windows
6. Fonts
7. Motion, reduced motion and faint effects

---

## 1. Ground rules

- Don't start, stop or restart a dev server someone else is running. Use it if it is up. If none is, ask, or run your own production preview on a different port and stop only that process afterwards.
- Don't capture the screen, focus or raise a window, or click on the real desktop.
- Don't point a check at a real database or app profile. Use mock data or a separate test database and profile.
- Use a temporary browser profile for every headless run, and kill only the processes you started (by PID), never every browser process on the machine.
- Keep screenshots and throwaway scripts in a temp or scratch folder, not in the repository.

## 2. Widths and overflow

Check at least these sizes, in every state the screen can be in (empty, one item, many, longest text, loading, error):

| Width x height | Stands for |
|---|---|
| 1440 x 900 | desktop |
| the app's fixed size | a desktop app window, if there is one |
| 768 x 1024 | tablet or narrow window |
| 390 x 844 | phone |

Run this in the page to list anything that overflows its box or the viewport:

```js
(() => {
  const vw = document.documentElement.clientWidth;
  const out = [];
  for (const el of document.querySelectorAll('body *')) {
    const r = el.getBoundingClientRect();
    if (r.width === 0 || r.height === 0) continue;
    if (r.right > vw + 0.5 || r.left < -0.5) out.push(['off-viewport', el]);
    const cs = getComputedStyle(el);
    if (el.scrollWidth > el.clientWidth + 1 && !/(auto|scroll)/.test(cs.overflowX)) out.push(['clipped-x', el]);
  }
  out.push(['page-scrolls-x', document.documentElement.scrollWidth > vw]);
  return out.map(([k, el]) => k + ' ' + (el.tagName ? el.tagName.toLowerCase() + (el.className ? '.' + String(el.className).split(' ').join('.') : '') : el));
})()
```

For a fixed-size app window, also assert `document.documentElement.scrollHeight <= innerHeight`: it should never scroll vertically either.

Then look at the captures yourself. The script cannot see a control that fits but looks crushed, a label that wraps to three lines, or a row of blocks made uneven by one long text.

## 3. Browser engines

Check in at least one Chromium engine and one other (Firefox, and WebKit where Safari matters). If the project has Playwright installed, it drives all three the same way:

```js
import { chromium, firefox, webkit } from 'playwright';
for (const type of [chromium, firefox, webkit]) {
  const browser = await type.launch();
  const page = await browser.newPage({ viewport: { width: 1440, height: 900 } });
  await page.goto('http://localhost:4321/page');
  await page.evaluate(() => document.fonts.ready);
  await page.screenshot({ path: `shot-${type.name()}-1440.png`, fullPage: true });
  await browser.close();
}
```

Without Playwright, the browsers' own headless modes work for a quick capture:

```bash
# Chromium or Edge
chrome --headless=new --user-data-dir="$TMP/chrome-profile" --window-size=1440,900 \
  --virtual-time-budget=4000 --hide-scrollbars --screenshot="$TMP/shot.png" "http://localhost:4321/page"

# Firefox
firefox --headless --no-remote --profile "$TMP/ff-profile" --window-size=1440,900 \
  --screenshot "$TMP/ff.png" "http://localhost:4321/page"
```

Known trap: Firefox's quick capture can fire before web fonts load and show a page with no text. That is not a broken page. Confirm with a second engine, or drive the browser properly (Playwright, or WebDriver BiDi through `--remote-debugging-port`) and wait for `document.fonts.ready`.

Judge these in every engine, not only Chromium: motion (especially slow movement of images, which can shimmer or step), anything scroll-driven (CSS scroll timelines are not supported everywhere), faint textures and light layers (banding on dark fades, pixelated blurs), and small text rendering.

## 4. A stale dev server

Dev server file watchers can lag, so a page may render with the previous stylesheet after an edit. Before capturing a change:

1. Touch the edited file and wait a second.
2. Request the page, find the stylesheet or module URL it loads, fetch that, and confirm it contains your change (search for a distinctive new value).
3. Only then capture.

If a page goes blank with module or MIME-type errors after you ran a production build in the same project, the bundler's dependency cache under the running dev server was probably rewritten. Ask the person running it to restart it.

## 5. Desktop app windows

For a desktop app (Tauri, Electron, WebView2, WKWebView):

- Launch it on a scratch profile or data directory, off-screen and unfocused if the app supports it. Look for the project's own switches first.
- Drive it through its web view's DevTools port rather than by clicking: `--remote-debugging-port=<port>` for Electron; `WEBVIEW2_ADDITIONAL_BROWSER_ARGUMENTS=--remote-debugging-port=<port>` for WebView2 on Windows (repeat any browser arguments the app sets itself, since the variable may replace them).
- Capture the window by its handle, never the screen area. On Windows use `PrintWindow` with the full-content flag; on macOS, `screencapture -l <windowid> out.png`.

Windows example, in PowerShell:

```powershell
Add-Type -ReferencedAssemblies System.Drawing -TypeDefinition @'
using System; using System.Drawing; using System.Runtime.InteropServices;
public static class Cap {
  [DllImport("user32.dll")] static extern bool GetWindowRect(IntPtr h, out RECT r);
  [DllImport("user32.dll")] static extern bool PrintWindow(IntPtr h, IntPtr dc, uint f);
  public struct RECT { public int L, T, R, B; }
  public static void Save(IntPtr h, string path) {
    RECT r; GetWindowRect(h, out r);
    using (var bmp = new Bitmap(r.R - r.L, r.B - r.T)) {
      using (var g = Graphics.FromImage(bmp)) { var dc = g.GetHdc(); PrintWindow(h, dc, 2); g.ReleaseHdc(dc); }
      bmp.Save(path);
    }
  }
}
'@
$p = Get-Process -Id <PID>   # the instance you launched
[Cap]::Save($p.MainWindowHandle, "$env:TEMP\app-shot.png")
```

`2` is `PW_RENDERFULLCONTENT`, needed for GPU-drawn web content. Stop only the instance you launched.

## 6. Fonts

A build or a page that looks fine proves nothing about fonts. Read the files.

Coverage, case shapes and the weight axis, with fontTools:

```python
from fontTools.ttLib import TTFont
f = TTFont("face.woff2")
cmap = f.getBestCmap()
need = set("every character the product renders, in every script it writes")
print("missing:", sorted(hex(ord(c)) for c in need if ord(c) not in cmap and not c.isspace()))
hm = f["hmtx"].metrics
for a, b in [("A", "a"), ("M", "m"), ("O", "o")]:
    print(a, b, hm[cmap[ord(a)]][0], hm[cmap[ord(b)]][0])  # equal widths: suspect a unicase face
os2 = f["OS/2"]
print("xHeight", getattr(os2, "sxHeight", None), "capHeight", getattr(os2, "sCapHeight", None))  # x-height above cap height: lowercase slots hold capitals
if "fvar" in f:
    for ax in f["fvar"].axes:
        print(ax.axisTag, ax.minValue, ax.defaultValue, ax.maxValue)  # the default is what a consumer ignoring the axis gets
```

On the page, check that each face in `document.fonts` has `status === 'loaded'`, and that every non-ASCII character used falls inside a declared `unicode-range` with a file behind it. Better still, make that a build check.

In PDFs, read back the embedded font names after any font change (`pdffonts file.pdf`, or list the `/FontName` entries with pypdf). A silent build can embed the wrong instance or a system fallback.

## 7. Motion, reduced motion and faint effects

- Emulate `prefers-reduced-motion: reduce` (Playwright `page.emulateMedia({ reducedMotion: 'reduce' })`, or the DevTools rendering panel) and confirm entrances are instant and nothing essential is lost. A busy spinner may stay.
- Confirm only `transform` and `opacity` are animated: no layout properties, filters or blur in any `transition` or `@keyframes`.
- Confirm hover styles sit inside `@media (hover: hover)`, so a tap on a phone does not leave an element lit.
- Tab through every control. The focus ring is visible, offset from the element and follows its corners. Anything draggable has a keyboard path.
- For a faint layer (texture, light, relief), sample pixels from a capture where the layer is and is not. A difference of one or two 8-bit levels will be invisible on some screens and banded on others. Aim for visible but quiet.
- For slow motion of images, capture several frames in each engine and compare their sharpness. Stepping, or pulsing between sharp and soft, means drop the effect rather than blur the image to hide it.
