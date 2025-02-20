EN

<!-- -->

We want to make this open-source project available for people all around the world.

[Help to translate](https://javascript.info/translate) the content of this tutorial to your language!

Search

Search

<a href="/tutorial/map" class="map"><span class="map__text">Tutorial map</span></a>

<span class="share-icons__title">Share</span><a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Fregexp-escaping" class="share share_tw"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Fregexp-escaping" class="share share_fb"></a>

1.  <a href="/" class="breadcrumbs__link"><span class="breadcrumbs__hidden-text">Tutorial</span></a>
2.  <span id="breadcrumb-1"><a href="/regular-expressions" class="breadcrumbs__link"><span>Regular expressions</span></a></span>

25th October 2021

# Escaping, special characters

As we’ve seen, a backslash `\` is used to denote character classes, e.g. `\d`. So it’s a special character in regexps (just like in regular strings).

There are other special characters as well, that have special meaning in a regexp, such as `[ ] { } ( ) \ ^ $ . | ? * +`. They are used to do more powerful searches.

Don’t try to remember the list – soon we’ll deal with each of them, and you’ll know them by heart automatically.

## <a href="#escaping" id="escaping" class="main__anchor">Escaping</a>

Let’s say we want to find literally a dot. Not “any character”, but just a dot.

To use a special character as a regular one, prepend it with a backslash: `\.`.

That’s also called “escaping a character”.

For example:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    alert( "Chapter 5.1".match(/\d\.\d/) ); // 5.1 (match!)
    alert( "Chapter 511".match(/\d\.\d/) ); // null (looking for a real dot \.)

Parentheses are also special characters, so if we want them, we should use `\(`. The example below looks for a string `"g()"`:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    alert( "function g()".match(/g\(\)/) ); // "g()"

If we’re looking for a backslash `\`, it’s a special character in both regular strings and regexps, so we should double it.

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    alert( "1\\2".match(/\\/) ); // '\'

## <a href="#a-slash" id="a-slash" class="main__anchor">A slash</a>

A slash symbol `'/'` is not a special character, but in JavaScript it is used to open and close the regexp: `/...pattern.../`, so we should escape it too.

Here’s what a search for a slash `'/'` looks like:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    alert( "/".match(/\//) ); // '/'

On the other hand, if we’re not using `/.../`, but create a regexp using `new RegExp`, then we don’t need to escape it:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    alert( "/".match(new RegExp("/")) ); // finds /

## <a href="#new-regexp" id="new-regexp" class="main__anchor">new RegExp</a>

If we are creating a regular expression with `new RegExp`, then we don’t have to escape `/`, but need to do some other escaping.

For instance, consider this:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let regexp = new RegExp("\d\.\d");

    alert( "Chapter 5.1".match(regexp) ); // null

The similar search in one of previous examples worked with `/\d\.\d/`, but `new RegExp("\d\.\d")` doesn’t work, why?

The reason is that backslashes are “consumed” by a string. As we may recall, regular strings have their own special characters, such as `\n`, and a backslash is used for escaping.

Here’s how “\\d.\\d” is perceived:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    alert("\d\.\d"); // d.d

String quotes “consume” backslashes and interpret them on their own, for instance:

- `\n` – becomes a newline character,
- `\u1234` – becomes the Unicode character with such code,
- …And when there’s no special meaning: like `\d` or `\z`, then the backslash is simply removed.

So `new RegExp` gets a string without backslashes. That’s why the search doesn’t work!

To fix it, we need to double backslashes, because string quotes turn `\\` into `\`:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let regStr = "\\d\\.\\d";
    alert(regStr); // \d\.\d (correct now)

    let regexp = new RegExp(regStr);

    alert( "Chapter 5.1".match(regexp) ); // 5.1

## <a href="#summary" id="summary" class="main__anchor">Summary</a>

- To search for special characters `[ \ ^ $ . | ? * + ( )` literally, we need to prepend them with a backslash `\` (“escape them”).
- We also need to escape `/` if we’re inside `/.../` (but not inside `new RegExp`).
- When passing a string to `new RegExp`, we need to double backslashes `\\`, cause string quotes consume one of them.

<a href="/regexp-boundary" class="page__nav page__nav_prev"><span class="page__nav-text"><span class="page__nav-text-shortcut"></span></span><span class="page__nav-text-alternate">Previous lesson</span></a><a href="/regexp-character-sets-and-ranges" class="page__nav page__nav_next"><span class="page__nav-text"><span class="page__nav-text-shortcut"></span></span><span class="page__nav-text-alternate">Next lesson</span></a>

<span class="share-icons__title">Share</span><a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Fregexp-escaping" class="share share_tw"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Fregexp-escaping" class="share share_fb"></a>

<a href="/tutorial/map" class="map"><span class="map__text">Tutorial map</span></a>

## <a href="#comments" id="comments">Comments</a>

<span class="comments__read-before-link">read this before commenting…</span>

- If you have suggestions what to improve - please [submit a GitHub issue](https://github.com/javascript-tutorial/en.javascript.info/issues/new) or a pull request instead of commenting.
- If you can't understand something in the article – please elaborate.
- To insert few words of code, use the `<code>` tag, for several lines – wrap them in `<pre>` tag, for more than 10 lines – use a sandbox ([plnkr](https://plnkr.co/edit/?p=preview), [jsbin](https://jsbin.com), [codepen](http://codepen.io)…)

<a href="/tutorial/map" class="map"></a>

#### Chapter

- <a href="/regular-expressions" class="sidebar__link">Regular expressions</a>

#### Lesson navigation

- <a href="#escaping" class="sidebar__link">Escaping</a>
- <a href="#a-slash" class="sidebar__link">A slash</a>
- <a href="#new-regexp" class="sidebar__link">new RegExp</a>
- <a href="#summary" class="sidebar__link">Summary</a>

- <a href="#comments" class="sidebar__link">Comments</a>

Share

<a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Fregexp-escaping" class="share share_tw sidebar__share"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Fregexp-escaping" class="share share_fb sidebar__share"></a>

<a href="https://github.com/javascript-tutorial/en.javascript.info/blob/master/9-regular-expressions/07-regexp-escaping" class="sidebar__link">Edit on GitHub</a>

- © 2007—2021  Ilya Kantor
- <a href="/about" class="page-footer__link">about the project</a>
- <a href="/about#contact-us" class="page-footer__link">contact us</a>
- <a href="/terms" class="page-footer__link">terms of usage</a>
- <a href="/privacy" class="page-footer__link">privacy policy</a>
