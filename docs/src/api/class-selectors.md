# class: Selectors

Selectors can be used to install custom selector engines. See [Working with selectors](../selectors.md) for more
information.

## async method: Selectors.register

An example of registering selector engine that queries elements based on a tag name:

```js
const { selectors, firefox } = require('playwright');  // Or 'chromium' or 'webkit'.

(async () => {
  // Must be a function that evaluates to a selector engine instance.
  const createTagNameEngine = () => ({
    // Returns the first element matching given selector in the root's subtree.
    query(root, selector) {
      return root.querySelector(selector);
    },

    // Returns all elements matching given selector in the root's subtree.
    queryAll(root, selector) {
      return Array.from(root.querySelectorAll(selector));
    }
  });

  // Register the engine. Selectors will be prefixed with "tag=".
  await selectors.register('tag', createTagNameEngine);

  const browser = await firefox.launch();
  const page = await browser.newPage();
  await page.setContent(`<div><button>Click me</button></div>`);

  // Use the selector prefixed with its name.
  const button = page.locator('tag=button');
  // Combine it with other selector engines.
  await page.click('tag=div >> text="Click me"');
  // Can use it in any methods supporting selectors.
  const buttonCount = await page.locator('tag=button').count();

  await browser.close();
})();
```

```java
// Script that evaluates to a selector engine instance.
String createTagNameEngine = "{\n" +
  "  // Returns the first element matching given selector in the root's subtree.\n" +
  "  query(root, selector) {\n" +
  "    return root.querySelector(selector);\n" +
  "  },\n" +
  "  // Returns all elements matching given selector in the root's subtree.\n" +
  "  queryAll(root, selector) {\n" +
  "    return Array.from(root.querySelectorAll(selector));\n" +
  "  }\n" +
  "}";
// Register the engine. Selectors will be prefixed with "tag=".
playwright.selectors().register("tag", createTagNameEngine);
Browser browser = playwright.firefox().launch();
Page page = browser.newPage();
page.setContent("<div><button>Click me</button></div>");
// Use the selector prefixed with its name.
Locator button = page.locator("tag=button");
// Combine it with other selector engines.
page.click("tag=div >> text=\"Click me\"");
// Can use it in any methods supporting selectors.
int buttonCount = (int) page.locator("tag=button").count();
browser.close();
```

```python async
import asyncio
from playwright.async_api import async_playwright

async def run(playwright):
    tag_selector = """
      {
          // Returns the first element matching given selector in the root's subtree.
          query(root, selector) {
              return root.querySelector(selector);
          },
          // Returns all elements matching given selector in the root's subtree.
          queryAll(root, selector) {
              return Array.from(root.querySelectorAll(selector));
          }
      }"""

    # Register the engine. Selectors will be prefixed with "tag=".
    await playwright.selectors.register("tag", tag_selector)
    browser = await playwright.chromium.launch()
    page = await browser.new_page()
    await page.set_content('<div><button>Click me</button></div>')

    # Use the selector prefixed with its name.
    button = await page.query_selector('tag=button')
    # Combine it with other selector engines.
    await page.click('tag=div >> text="Click me"')
    # Can use it in any methods supporting selectors.
    button_count = await page.locator('tag=button').count()
    print(button_count)
    await browser.close()

async def main():
    async with async_playwright() as playwright:
        await run(playwright)

asyncio.run(main())
```

```python sync
from playwright.sync_api import sync_playwright

def run(playwright):
    tag_selector = """
      {
          // Returns the first element matching given selector in the root's subtree.
          query(root, selector) {
              return root.querySelector(selector);
          },
          // Returns all elements matching given selector in the root's subtree.
          queryAll(root, selector) {
              return Array.from(root.querySelectorAll(selector));
          }
      }"""

    # Register the engine. Selectors will be prefixed with "tag=".
    playwright.selectors.register("tag", tag_selector)
    browser = playwright.chromium.launch()
    page = browser.new_page()
    page.set_content('<div><button>Click me</button></div>')

    # Use the selector prefixed with its name.
    button = page.locator('tag=button')
    # Combine it with other selector engines.
    page.click('tag=div >> text="Click me"')
    # Can use it in any methods supporting selectors.
    button_count = page.locator('tag=button').count()
    print(button_count)
    browser.close()

with sync_playwright() as playwright:
    run(playwright)
```

```csharp
using var playwright = await Playwright.CreateAsync();
// Script that evaluates to a selector engine instance.
await playwright.Selectors.RegisterAsync("tag", @"{
// Returns the first element matching given selector in the root's subtree.
query(root, selector) {
    return root.querySelector(selector);
  },
  // Returns all elements matching given selector in the root's subtree.
  queryAll(root, selector) {
    return Array.from(root.querySelectorAll(selector));
  }
}");

await using var browser = await playwright.Chromium.LaunchAsync();
var page = await browser.NewPageAsync();
await page.SetContentAsync("<div><button>Click me</button></div>");
// Use the selector prefixed with its name.
var button = page.Locator("tag=button");
// Combine it with other selector engines.
await page.ClickAsync("tag=div >> text=\"Click me\"");
// Can use it in any methods supporting selectors.
int buttonCount = await page.Locator("tag=button").CountAsync();
```

### param: Selectors.register.name
- `name` <[string]>

Name that is used in selectors as a prefix, e.g. `{name: 'foo'}` enables `foo=myselectorbody` selectors. May only
contain `[a-zA-Z0-9_]` characters.

### param: Selectors.register.script
* langs: js
- `script` <[function]|[string]|[Object]>
  - `path` <[path]> Path to the JavaScript file. If `path` is a relative path, then it is resolved relative to the
    current working directory. Optional.
  - `content` <[string]> Raw script content. Optional.

Script that evaluates to a selector engine instance.

### param: Selectors.register.script
* langs: java
- `script` <[string]|[path]>

Script that evaluates to a selector engine instance.

### option: Selectors.register.script
* langs: csharp
- `script` <[string]>

Script that evaluates to a selector engine instance.

### option: Selectors.register.path
* langs: csharp
- `path` <[path]>

Script that evaluates to a selector engine instance.

### option: Selectors.register.contentScript
- `contentScript` <[boolean]>

Whether to run this selector engine in isolated JavaScript environment. This environment has access to the same DOM, but
not any JavaScript objects from the frame's scripts. Defaults to `false`. Note that running as a content script is not
guaranteed when this engine is used together with other registered engines.