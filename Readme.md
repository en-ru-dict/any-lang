README.md, переведенный на английский и стилизованный для международной аудитории GitHub.

**AnyLang** is a tiny (10kB, \~3kB minified) JavaScript library for internationalization (i18n). It was built for one purpose: to translate your **static site** in 10 minutes, not 10 days.

Forget `npm`, `webpack`, `babel`, server configurations, and complex APIs. If you have a landing page, a portfolio, a brochure site, or a small project without React/Vue, and you just need to add 2-3 languages—this is for you.

## 🚀 Why AnyLang? (And not the "monsters"?)

Let's be honest: traditional i18n is a pain.

  * **"Big" Frameworks (i18next, etc.):**
      * **Heavy:** Often 300kB+ with all the plugins.
      * **Require `tr()`:** You have to wrap *every single string* in your code with `tr('string')` or use a `t(key)`.
      * **Don't translate everything:** They often can't translate text in `placeholder`, `title`, or worse, strings generated *inside* your JavaScript (`alert('Hello')`).
  * **Build Tools (Babel, etc.):**
      * **Complex:** Require `npm`, `webpack`, tons of configuration, and CI/CD.
      * **Costly:** You need an experienced (and expensive) developer just to set it up and maintain it.
      * **Overkill:** Why do you need server-side rendering and a complex build process for a 5-page static site?

**AnyLang takes a different, simpler approach.** It's "brute-force," but it's incredibly effective.

-----

## ✨ Key Features

  * **Translates ABSOLUTELY Everything:**
      * HTML content.
      * Attributes: `placeholder`, `title`, etc. 
      * CSS content (via `::before` / `::after`).
      * **Strings inside your JavaScript:** `alert('[·Message·]')` will be translated! 
      * **Even HTML comments!** (Helpful for non-English developers debugging in F12). 
  * **Zero Dependencies:** No `npm install`. Just link two files. 
  * **No Server Needed:** Works perfectly on **any** static hosting (GitHub Pages, Netlify, Vercel, or a simple FTP folder).
  * **Auto-Updating Menu:** The language `<select>` menu is generated and updated automatically from your dictionary file. 
  * **Add Languages in 30 Seconds:** Just copy a text block, translate it, and paste it into `all_lang.txt`. Done. 

-----

## 🎩 How It Works (The Magic & The Compromise)

AnyLang uses a simple but powerful mechanism:

1.  It grabs the *entire* HTML of your page (`document.documentElement.innerHTML`). 
2.  It finds all strings wrapped in markers (e.g., `[·Hello World·]`). 
3.  It replaces them with the translated strings from your `all_lang.txt` dictionary. 
4.  It **completely rewrites the page's HTML** with the new, translated content (`document.documentElement.innerHTML = html`). 
5.  It **restarts all inline `<script>` tags** on the page (except those with a special stop-string). 

**Why is this cool?**
Because any strings in your *inline* scripts (like `alert('[·Welcome!·]')` or `document.write('[·Hello·]')`) are *also* replaced, and the script is re-executed in the new language! 

**What's the catch? (The Rules):**
This "brute-force" method is powerful, but it requires you to follow a few simple rules.

### 1\. The Marker Rule

All text you want to translate **must** be wrapped in markers. The defaults are `[·` and `·]`. 

  * **Use `helper.htm`:** The repository includes a helper tool to quickly wrap all text nodes on your page.

### 2\. The `window.var` Rule (No global `const`/`let`!)

Because inline scripts are re-executed, you will get an "Identifier has already been declared" error if you use `const` or `let` in the global scope.

> **BANNED:**
> `const API_KEY = '...';`
> `let user_count = 10;`
>
> **ALLOWED:** (Always use the `window` object for globals)
> `window.API_KEY = '...';`
> `window.user_count = 10;` 

### 3\. The Dynamic String Rule

Machine translators can't understand broken code strings. You must refactor them to use placeholders.

> **BAD:** (A translator will break `user.name`)
> `el.innerHTML = '[·Hello,·] ' + user.name + '[·!·]';`
>
> **GOOD:** (Use template literals and placeholders)
> `const s1 = user.name;`
> ` el.innerHTML =  `[·Hello, ${s1}!·]`;`
>
> In your dictionary, this is safe to translate:
> `10. Hello, ${s1}!`
> `10. ¡Hola, ${s1}!`

### 4\. The Pluralization Compromise

This library cannot handle complex pluralization rules (e.g., 1 `word`, 2 `words`, 5 `words`). This is a deliberate trade-off to keep it 3kB. For most brochure sites, this is not a critical issue.

-----

## 🛠️ Quick Start

1.  **Link the files:** Add `all_lang.txt` and `any_lang.js` to your HTML, right before the closing `</body>` tag. 
    ```html
    <script src="all_lang.txt"></script>
    <script src="any_lang.js"></script>
    </body>
    </html>
    ```
2.  **Wrap your strings:** Wrap *all* text in your base language (e.g., EN) with `[·...·]` markers. Don't forget `placeholder`, `title`, and strings inside your inline `<script>` tags.
3.  **Add the `<select>`:** Add the language selector anywhere on your page. It will be enabled and populated automatically. 
    ```html
    <select disabled id="id_lang">
      <option value="EN">EN</option>
    </select>
    ```
4.  **Fill the dictionary:** Open `all_lang.txt` and populate the `### EN` section with all your wrapped strings, one per line, with a number. 

## ➕ How to Add a New Language (in 30 seconds)

This is the best part.

1.  Open `all_lang.txt`. 
2.  Copy the *entire* `### EN (english)` section, including the header. 
3.  Paste it at the end of the file.
4.  Change the header to, for example, `### FR (français)`. 
5.  **Translate only the text** after the number (e.g., ` 1.  `, ` 2.  `) . Do not touch any `${s1}` placeholders.
6.  Save the file.

**That's it!** Reload your page. The new language will automatically appear in the dropdown. 

-----

## 💡 Optimization Tips

  * **Extract non-UI code:** Any JS that does **not** need to be restarted (e.g., Google Analytics, jQuery plugins, complex math) should be moved to external files (`<script src="..."></script>`). **External scripts are NOT restarted.** 
  * **Keep UI code inline:** Leave only the scripts that draw your UI or show messages to the user inside inline `<script>` tags.
  * **F12 vs. "View Source":**
      * **"View Source"** (Ctrl+U) will show your page *before* translation, with all the `[·...·]` markers.
      * **F12 Inspector** (Dev Tools) will show the *live, translated* HTML, including translated comments and JS strings. This is incredibly useful for debugging.

## 🎯 The Verdict

**AnyLang** is not a replacement for React-i18n in a complex web application.

It's a "guerilla" tool for developers who need it to "just work"—**yesterday**. It's for your landing page, your portfolio, and your open-source project that deserves to be understood in more than one country.

Stop overcomplicating. Take it, use it, fork it, and improve it\!
