EN

- <a href="https://ar.javascript.info/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">AR</span><span class="supported-langs__title">عربي</span></a>
- <a href="https://javascript.info/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">EN</span><span class="supported-langs__title">English</span></a>
- <a href="https://es.javascript.info/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">ES</span><span class="supported-langs__title">Español</span></a>
- <a href="https://fr.javascript.info/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">FR</span><span class="supported-langs__title">Français</span></a>
- <a href="https://id.javascript.info/" class="supported-langs__link"><span class="supported-langs__brief">ID</span><span class="supported-langs__title">Indonesia</span></a>
- <a href="https://it.javascript.info/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">IT</span><span class="supported-langs__title">Italiano</span></a>

<!-- -->

- <a href="https://ja.javascript.info/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">JA</span><span class="supported-langs__title">日本語</span></a>
- <a href="https://ko.javascript.info/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">KO</span><span class="supported-langs__title">한국어</span></a>
- <a href="https://learn.javascript.ru/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">RU</span><span class="supported-langs__title">Русский</span></a>
- <a href="https://tr.javascript.info/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">TR</span><span class="supported-langs__title">Türkçe</span></a>
- <a href="https://zh.javascript.info/task/error-nonexisting" class="supported-langs__link"><span class="supported-langs__brief">ZH</span><span class="supported-langs__title">简体中文</span></a>

We want to make this open-source project available for people all around the world.

[Help to translate](https://javascript.info/translate) the content of this tutorial to your language!

<a href="/" class="sitetoolbar__link sitetoolbar__link_logo"><img src="/img/sitetoolbar__logo_en.svg" class="sitetoolbar__logo sitetoolbar__logo_normal" role="presentation" width="200" /><img src="/img/sitetoolbar__logo_small_en.svg" class="sitetoolbar__logo sitetoolbar__logo_small" role="presentation" width="70" /></a>

<a href="/ebook" class="buy-book-button"><span class="buy-book-button__extra-text">Buy</span>EPUB/PDF</a>

Search

Search

<a href="/tutorial/map" class="map"><span class="map__text">Tutorial map</span></a>

<span class="share-icons__title">Share</span><a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Ftask%2Ferror-nonexisting" class="share share_tw"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Ftask%2Ferror-nonexisting" class="share share_fb"></a>

عربيEnglishEspañolFrançaisIndonesiaItaliano 日本語한국어РусскийTürkçe 简体中文

<a href="/" class="breadcrumbs__link"><span class="breadcrumbs__hidden-text">Tutorial</span></a>

<a href="/js" class="breadcrumbs__link"><span>The JavaScript language</span></a>

<a href="/js-misc" class="breadcrumbs__link"><span>Miscellaneous</span></a>

<a href="/proxy" class="breadcrumbs__link"><span>Proxy and Reflect</span></a>

<a href="/proxy" class="task-single__back"><span>back to the lesson</span></a>

## Error on reading non-existent property

Usually, an attempt to read a non-existent property returns `undefined`.

Create a proxy that throws an error for an attempt to read of a non-existent property instead.

That can help to detect programming mistakes early.

Write a function `wrap(target)` that takes an object `target` and return a proxy that adds this functionality aspect.

That’s how it should work:

    let user = {
      name: "John"
    };

    function wrap(target) {
      return new Proxy(target, {
          /* your code */
      });
    }

    user = wrap(user);

    alert(user.name); // John
    alert(user.age); // ReferenceError: Property doesn't exist: "age"

solution

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let user = {
      name: "John"
    };

    function wrap(target) {
      return new Proxy(target, {
        get(target, prop, receiver) {
          if (prop in target) {
            return Reflect.get(target, prop, receiver);
          } else {
            throw new ReferenceError(`Property doesn't exist: "${prop}"`)
          }
        }
      });
    }

    user = wrap(user);

    alert(user.name); // John
    alert(user.age); // ReferenceError: Property doesn't exist: "age"

- © 2007—2021  Ilya Kantor
- <a href="/about" class="page-footer__link">about the project</a>
- <a href="/about#contact-us" class="page-footer__link">contact us</a>
- <a href="/terms" class="page-footer__link">terms of usage</a>
- <a href="/privacy" class="page-footer__link">privacy policy</a>
