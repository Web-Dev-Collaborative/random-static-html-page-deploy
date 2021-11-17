EN


<!-- -->


We want to make this open-source project available for people all around the world.

[Help to translate](https://javascript.info/translate) the content of this tutorial to your language!



Search

Search

<a href="/tutorial/map" class="map"><span class="map__text">Tutorial map</span></a>

<span class="share-icons__title">Share</span><a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Fregexp-unicode" class="share share_tw"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Fregexp-unicode" class="share share_fb"></a>


1.  <a href="/" class="breadcrumbs__link"><span class="breadcrumbs__hidden-text">Tutorial</span></a>
2.  <span id="breadcrumb-1"><a href="/regular-expressions" class="breadcrumbs__link"><span>Regular expressions</span></a></span>

7th June 2021

# Unicode: flag "u" and class \\p{...}

JavaScript uses [Unicode encoding](https://en.wikipedia.org/wiki/Unicode) for strings. Most characters are encoded with 2 bytes, but that allows to represent at most 65536 characters.

That range is not big enough to encode all possible characters, that’s why some rare characters are encoded with 4 bytes, for instance like `𝒳` (mathematical X) or `😄` (a smile), some hieroglyphs and so on.

Here are the Unicode values of some characters:

<table><thead><tr class="header"><th>Character</th><th>Unicode</th><th>Bytes count in Unicode</th></tr></thead><tbody><tr class="odd"><td>a</td><td><code>0x0061</code></td><td>2</td></tr><tr class="even"><td>≈</td><td><code>0x2248</code></td><td>2</td></tr><tr class="odd"><td>𝒳</td><td><code>0x1d4b3</code></td><td>4</td></tr><tr class="even"><td>𝒴</td><td><code>0x1d4b4</code></td><td>4</td></tr><tr class="odd"><td>😄</td><td><code>0x1f604</code></td><td>4</td></tr></tbody></table>

So characters like `a` and `≈` occupy 2 bytes, while codes for `𝒳`, `𝒴` and `😄` are longer, they have 4 bytes.

Long time ago, when JavaScript language was created, Unicode encoding was simpler: there were no 4-byte characters. So, some language features still handle them incorrectly.

For instance, `length` thinks that here are two characters:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    alert('😄'.length); // 2
    alert('𝒳'.length); // 2

…But we can see that there’s only one, right? The point is that `length` treats 4 bytes as two 2-byte characters. That’s incorrect, because they must be considered only together (so-called “surrogate pair”, you can read about them in the article [Strings](/string)).

By default, regular expressions also treat 4-byte “long characters” as a pair of 2-byte ones. And, as it happens with strings, that may lead to odd results. We’ll see that a bit later, in the article [Sets and ranges \[...\]](/regexp-character-sets-and-ranges).

Unlike strings, regular expressions have flag `u` that fixes such problems. With such flag, a regexp handles 4-byte characters correctly. And also Unicode property search becomes available, we’ll get to it next.

## <a href="#unicode-properties-p" id="unicode-properties-p" class="main__anchor">Unicode properties \p{…}</a>

Every character in Unicode has a lot of properties. They describe what “category” the character belongs to, contain miscellaneous information about it.

For instance, if a character has `Letter` property, it means that the character belongs to an alphabet (of any language). And `Number` property means that it’s a digit: maybe Arabic or Chinese, and so on.

We can search for characters with a property, written as `\p{…}`. To use `\p{…}`, a regular expression must have flag `u`.

For instance, `\p{Letter}` denotes a letter in any language. We can also use `\p{L}`, as `L` is an alias of `Letter`. There are shorter aliases for almost every property.

In the example below three kinds of letters will be found: English, Georgian and Korean.

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let str = "A ბ ㄱ";

    alert( str.match(/\p{L}/gu) ); // A,ბ,ㄱ
    alert( str.match(/\p{L}/g) ); // null (no matches, \p doesn't work without the flag "u")

Here’s the main character categories and their subcategories:

-   Letter `L`:
    -   lowercase `Ll`
    -   modifier `Lm`,
    -   titlecase `Lt`,
    -   uppercase `Lu`,
    -   other `Lo`.
-   Number `N`:
    -   decimal digit `Nd`,
    -   letter number `Nl`,
    -   other `No`.
-   Punctuation `P`:
    -   connector `Pc`,
    -   dash `Pd`,
    -   initial quote `Pi`,
    -   final quote `Pf`,
    -   open `Ps`,
    -   close `Pe`,
    -   other `Po`.
-   Mark `M` (accents etc):
    -   spacing combining `Mc`,
    -   enclosing `Me`,
    -   non-spacing `Mn`.
-   Symbol `S`:
    -   currency `Sc`,
    -   modifier `Sk`,
    -   math `Sm`,
    -   other `So`.
-   Separator `Z`:
    -   line `Zl`,
    -   paragraph `Zp`,
    -   space `Zs`.
-   Other `C`:
    -   control `Cc`,
    -   format `Cf`,
    -   not assigned `Cn`,
    -   private use `Co`,
    -   surrogate `Cs`.

So, e.g. if we need letters in lower case, we can write `\p{Ll}`, punctuation signs: `\p{P}` and so on.

There are also other derived categories, like:

-   `Alphabetic` (`Alpha`), includes Letters `L`, plus letter numbers `Nl` (e.g. Ⅻ – a character for the roman number 12), plus some other symbols `Other_Alphabetic` (`OAlpha`).
-   `Hex_Digit` includes hexadecimal digits: `0-9`, `a-f`.
-   …And so on.

Unicode supports many different properties, their full list would require a lot of space, so here are the references:

-   List all properties by a character: <https://unicode.org/cldr/utility/character.jsp>.
-   List all characters by a property: <https://unicode.org/cldr/utility/list-unicodeset.jsp>.
-   Short aliases for properties: <https://www.unicode.org/Public/UCD/latest/ucd/PropertyValueAliases.txt>.
-   A full base of Unicode characters in text format, with all properties, is here: <https://www.unicode.org/Public/UCD/latest/ucd/>.

### <a href="#example-hexadecimal-numbers" id="example-hexadecimal-numbers" class="main__anchor">Example: hexadecimal numbers</a>

For instance, let’s look for hexadecimal numbers, written as `xFF`, where `F` is a hex digit (0…9 or A…F).

A hex digit can be denoted as `\p{Hex_Digit}`:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let regexp = /x\p{Hex_Digit}\p{Hex_Digit}/u;

    alert("number: xAF".match(regexp)); // xAF

### <a href="#example-chinese-hieroglyphs" id="example-chinese-hieroglyphs" class="main__anchor">Example: Chinese hieroglyphs</a>

Let’s look for Chinese hieroglyphs.

There’s a Unicode property `Script` (a writing system), that may have a value: `Cyrillic`, `Greek`, `Arabic`, `Han` (Chinese) and so on, [here’s the full list](https://en.wikipedia.org/wiki/Script_(Unicode)).

To look for characters in a given writing system we should use `Script=<value>`, e.g. for Cyrillic letters: `\p{sc=Cyrillic}`, for Chinese hieroglyphs: `\p{sc=Han}`, and so on:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let regexp = /\p{sc=Han}/gu; // returns Chinese hieroglyphs

    let str = `Hello Привет 你好 123_456`;

    alert( str.match(regexp) ); // 你,好

### <a href="#example-currency" id="example-currency" class="main__anchor">Example: currency</a>

Characters that denote a currency, such as `$`, `€`, `¥`, have Unicode property `\p{Currency_Symbol}`, the short alias: `\p{Sc}`.

Let’s use it to look for prices in the format “currency, followed by a digit”:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let regexp = /\p{Sc}\d/gu;

    let  str = `Prices: $2, €1, ¥9`;

    alert( str.match(regexp) ); // $2,€1,¥9

Later, in the article [Quantifiers +, \*, ? and {n}](/regexp-quantifiers) we’ll see how to look for numbers that contain many digits.

## <a href="#summary" id="summary" class="main__anchor">Summary</a>

Flag `u` enables the support of Unicode in regular expressions.

That means two things:

1.  Characters of 4 bytes are handled correctly: as a single character, not two 2-byte characters.
2.  Unicode properties can be used in the search: `\p{…}`.

With Unicode properties we can look for words in given languages, special characters (quotes, currencies) and so on.

<a href="/regexp-character-classes" class="page__nav page__nav_prev"><span class="page__nav-text"><span class="page__nav-text-shortcut"></span></span><span class="page__nav-text-alternate">Previous lesson</span></a><a href="/regexp-anchors" class="page__nav page__nav_next"><span class="page__nav-text"><span class="page__nav-text-shortcut"></span></span><span class="page__nav-text-alternate">Next lesson</span></a>

<span class="share-icons__title">Share</span><a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Fregexp-unicode" class="share share_tw"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Fregexp-unicode" class="share share_fb"></a>

<a href="/tutorial/map" class="map"><span class="map__text">Tutorial map</span></a>

## <a href="#comments" id="comments">Comments</a>

<span class="comments__read-before-link">read this before commenting…</span>

-   If you have suggestions what to improve - please [submit a GitHub issue](https://github.com/javascript-tutorial/en.javascript.info/issues/new) or a pull request instead of commenting.
-   If you can't understand something in the article – please elaborate.
-   To insert few words of code, use the `<code>` tag, for several lines – wrap them in `<pre>` tag, for more than 10 lines – use a sandbox ([plnkr](https://plnkr.co/edit/?p=preview), [jsbin](https://jsbin.com), [codepen](http://codepen.io)…)

<a href="/tutorial/map" class="map"></a>

#### Chapter

-   <a href="/regular-expressions" class="sidebar__link">Regular expressions</a>

#### Lesson navigation

-   <a href="#unicode-properties-p" class="sidebar__link">Unicode properties \p{…}</a>
-   <a href="#summary" class="sidebar__link">Summary</a>

-   <a href="#comments" class="sidebar__link">Comments</a>

Share

<a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Fregexp-unicode" class="share share_tw sidebar__share"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Fregexp-unicode" class="share share_fb sidebar__share"></a>

<a href="https://github.com/javascript-tutorial/en.javascript.info/blob/master/9-regular-expressions/03-regexp-unicode" class="sidebar__link">Edit on GitHub</a>

-   © 2007—2021  Ilya Kantor
-   <a href="/about" class="page-footer__link">about the project</a>
-   <a href="/about#contact-us" class="page-footer__link">contact us</a>
-   <a href="/terms" class="page-footer__link">terms of usage</a>
-   <a href="/privacy" class="page-footer__link">privacy policy</a>
