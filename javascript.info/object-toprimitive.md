EN

<!-- -->

We want to make this open-source project available for people all around the world.

[Help to translate](https://javascript.info/translate) the content of this tutorial to your language!

Search

Search

<a href="/tutorial/map" class="map"><span class="map__text">Tutorial map</span></a>

<span class="share-icons__title">Share</span><a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Fobject-toprimitive" class="share share_tw"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Fobject-toprimitive" class="share share_fb"></a>

1.  <a href="/" class="breadcrumbs__link"><span class="breadcrumbs__hidden-text">Tutorial</span></a>
2.  <span id="breadcrumb-1"><a href="/js" class="breadcrumbs__link"><span>The JavaScript language</span></a></span>
3.  <span id="breadcrumb-2"><a href="/object-basics" class="breadcrumbs__link"><span>Objects: the basics</span></a></span>

20th June 2021

# Object to primitive conversion

What happens when objects are added `obj1 + obj2`, subtracted `obj1 - obj2` or printed using `alert(obj)`?

JavaScript doesn’t exactly allow to customize how operators work on objects. Unlike some other programming languages, such as Ruby or C++, we can’t implement a special object method to handle an addition (or other operators).

In case of such operations, objects are auto-converted to primitives, and then the operation is carried out over these primitives and results in a primitive value.

That’s an important limitation, as the result of `obj1 + obj2` can’t be another object!

E.g. we can’t make objects representing vectors or matrices (or achievements or whatever), add them and expect a “summed” object as the result. Such architectural feats are automatically “off the board”.

So, because we can’t do much here, there’s no maths with objects in real projects. When it happens, it’s usually because of a coding mistake.

In this chapter we’ll cover how an object converts to primitive and how to customize it.

We have two purposes:

1.  It will allow us to understand what’s going on in case of coding mistakes, when such an operation happened accidentally.
2.  There are exceptions, where such operations are possible and look good. E.g. subtracting or comparing dates (`Date` objects). We’ll come across them later.

## <a href="#conversion-rules" id="conversion-rules" class="main__anchor">Conversion rules</a>

In the chapter [Type Conversions](/type-conversions) we’ve seen the rules for numeric, string and boolean conversions of primitives. But we left a gap for objects. Now, as we know about methods and symbols it becomes possible to fill it.

1.  All objects are `true` in a boolean context. There are only numeric and string conversions.
2.  The numeric conversion happens when we subtract objects or apply mathematical functions. For instance, `Date` objects (to be covered in the chapter [Date and time](/date)) can be subtracted, and the result of `date1 - date2` is the time difference between two dates.
3.  As for the string conversion – it usually happens when we output an object like `alert(obj)` and in similar contexts.

We can fine-tune string and numeric conversion, using special object methods.

There are three variants of type conversion, that happen in various situations.

They’re called “hints”, as described in the [specification](https://tc39.github.io/ecma262/#sec-toprimitive):

`"string"`  
For an object-to-string conversion, when we’re doing an operation on an object that expects a string, like `alert`:

    // output
    alert(obj);

    // using object as a property key
    anotherObj[obj] = 123;

`"number"`  
For an object-to-number conversion, like when we’re doing maths:

    // explicit conversion
    let num = Number(obj);

    // maths (except binary plus)
    let n = +obj; // unary plus
    let delta = date1 - date2;

    // less/greater comparison
    let greater = user1 > user2;

`"default"`  
Occurs in rare cases when the operator is “not sure” what type to expect.

For instance, binary plus `+` can work both with strings (concatenates them) and numbers (adds them), so both strings and numbers would do. So if a binary plus gets an object as an argument, it uses the `"default"` hint to convert it.

Also, if an object is compared using `==` with a string, number or a symbol, it’s also unclear which conversion should be done, so the `"default"` hint is used.

    // binary plus uses the "default" hint
    let total = obj1 + obj2;

    // obj == number uses the "default" hint
    if (user == 1) { ... };

The greater and less comparison operators, such as `<` `>`, can work with both strings and numbers too. Still, they use the `"number"` hint, not `"default"`. That’s for historical reasons.

In practice though, we don’t need to remember these peculiar details, because all built-in objects except for one case (`Date` object, we’ll learn it later) implement `"default"` conversion the same way as `"number"`. And we can do the same.

<span class="important__type">No `"boolean"` hint</span>

Please note – there are only three hints. It’s that simple.

There is no “boolean” hint (all objects are `true` in boolean context) or anything else. And if we treat `"default"` and `"number"` the same, like most built-ins do, then there are only two conversions.

**To do the conversion, JavaScript tries to find and call three object methods:**

1.  Call `obj[Symbol.toPrimitive](hint)` – the method with the symbolic key `Symbol.toPrimitive` (system symbol), if such method exists,
2.  Otherwise if hint is `"string"`
    - try `obj.toString()` and `obj.valueOf()`, whatever exists.
3.  Otherwise if hint is `"number"` or `"default"`
    - try `obj.valueOf()` and `obj.toString()`, whatever exists.

## <a href="#symbol-toprimitive" id="symbol-toprimitive" class="main__anchor">Symbol.toPrimitive</a>

Let’s start from the first method. There’s a built-in symbol named `Symbol.toPrimitive` that should be used to name the conversion method, like this:

    obj[Symbol.toPrimitive] = function(hint) {
      // here goes the code to convert this object to a primitive
      // it must return a primitive value
      // hint = one of "string", "number", "default"
    };

If the method `Symbol.toPrimitive` exists, it’s used for all hints, and no more methods are needed.

For instance, here `user` object implements it:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let user = {
      name: "John",
      money: 1000,

      [Symbol.toPrimitive](hint) {
        alert(`hint: ${hint}`);
        return hint == "string" ? `{name: "${this.name}"}` : this.money;
      }
    };

    // conversions demo:
    alert(user); // hint: string -> {name: "John"}
    alert(+user); // hint: number -> 1000
    alert(user + 500); // hint: default -> 1500

As we can see from the code, `user` becomes a self-descriptive string or a money amount depending on the conversion. The single method `user[Symbol.toPrimitive]` handles all conversion cases.

## <a href="#tostring-valueof" id="tostring-valueof" class="main__anchor">toString/valueOf</a>

If there’s no `Symbol.toPrimitive` then JavaScript tries to find methods `toString` and `valueOf`:

- For the “string” hint: `toString`, and if it doesn’t exist, then `valueOf` (so `toString` has the priority for string conversions).
- For other hints: `valueOf`, and if it doesn’t exist, then `toString` (so `valueOf` has the priority for maths).

Methods `toString` and `valueOf` come from ancient times. They are not symbols (symbols did not exist that long ago), but rather “regular” string-named methods. They provide an alternative “old-style” way to implement the conversion.

These methods must return a primitive value. If `toString` or `valueOf` returns an object, then it’s ignored (same as if there were no method).

By default, a plain object has following `toString` and `valueOf` methods:

- The `toString` method returns a string `"[object Object]"`.
- The `valueOf` method returns the object itself.

Here’s the demo:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let user = {name: "John"};

    alert(user); // [object Object]
    alert(user.valueOf() === user); // true

So if we try to use an object as a string, like in an `alert` or so, then by default we see `[object Object]`.

The default `valueOf` is mentioned here only for the sake of completeness, to avoid any confusion. As you can see, it returns the object itself, and so is ignored. Don’t ask me why, that’s for historical reasons. So we can assume it doesn’t exist.

Let’s implement these methods to customize the conversion.

For instance, here `user` does the same as above using a combination of `toString` and `valueOf` instead of `Symbol.toPrimitive`:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let user = {
      name: "John",
      money: 1000,

      // for hint="string"
      toString() {
        return `{name: "${this.name}"}`;
      },

      // for hint="number" or "default"
      valueOf() {
        return this.money;
      }

    };

    alert(user); // toString -> {name: "John"}
    alert(+user); // valueOf -> 1000
    alert(user + 500); // valueOf -> 1500

As we can see, the behavior is the same as the previous example with `Symbol.toPrimitive`.

Often we want a single “catch-all” place to handle all primitive conversions. In this case, we can implement `toString` only, like this:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let user = {
      name: "John",

      toString() {
        return this.name;
      }
    };

    alert(user); // toString -> John
    alert(user + 500); // toString -> John500

In the absence of `Symbol.toPrimitive` and `valueOf`, `toString` will handle all primitive conversions.

### <a href="#a-conversion-can-return-any-primitive-type" id="a-conversion-can-return-any-primitive-type" class="main__anchor">A conversion can return any primitive type</a>

The important thing to know about all primitive-conversion methods is that they do not necessarily return the “hinted” primitive.

There is no control whether `toString` returns exactly a string, or whether `Symbol.toPrimitive` method returns a number for a hint `"number"`.

The only mandatory thing: these methods must return a primitive, not an object.

<span class="important__type">Historical notes</span>

For historical reasons, if `toString` or `valueOf` returns an object, there’s no error, but such value is ignored (like if the method didn’t exist). That’s because in ancient times there was no good “error” concept in JavaScript.

In contrast, `Symbol.toPrimitive` _must_ return a primitive, otherwise there will be an error.

## <a href="#further-conversions" id="further-conversions" class="main__anchor">Further conversions</a>

As we know already, many operators and functions perform type conversions, e.g. multiplication `*` converts operands to numbers.

If we pass an object as an argument, then there are two stages:

1.  The object is converted to a primitive (using the rules described above).
2.  If the resulting primitive isn’t of the right type, it’s converted.

For instance:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let obj = {
      // toString handles all conversions in the absence of other methods
      toString() {
        return "2";
      }
    };

    alert(obj * 2); // 4, object converted to primitive "2", then multiplication made it a number

1.  The multiplication `obj * 2` first converts the object to primitive (that’s a string `"2"`).
2.  Then `"2" * 2` becomes `2 * 2` (the string is converted to number).

Binary plus will concatenate strings in the same situation, as it gladly accepts a string:

<a href="#" class="toolbar__button toolbar__button_run" title="run"></a>

<a href="#" class="toolbar__button toolbar__button_edit" title="open in sandbox"></a>

    let obj = {
      toString() {
        return "2";
      }
    };

    alert(obj + 2); // 22 ("2" + 2), conversion to primitive returned a string => concatenation

## <a href="#summary" id="summary" class="main__anchor">Summary</a>

The object-to-primitive conversion is called automatically by many built-in functions and operators that expect a primitive as a value.

There are 3 types (hints) of it:

- `"string"` (for `alert` and other operations that need a string)
- `"number"` (for maths)
- `"default"` (few operators)

The specification describes explicitly which operator uses which hint. There are very few operators that “don’t know what to expect” and use the `"default"` hint. Usually for built-in objects `"default"` hint is handled the same way as `"number"`, so in practice the last two are often merged together.

The conversion algorithm is:

1.  Call `obj[Symbol.toPrimitive](hint)` if the method exists,
2.  Otherwise if hint is `"string"`
    - try `obj.toString()` and `obj.valueOf()`, whatever exists.
3.  Otherwise if hint is `"number"` or `"default"`
    - try `obj.valueOf()` and `obj.toString()`, whatever exists.

In practice, it’s often enough to implement only `obj.toString()` as a “catch-all” method for string conversions that should return a “human-readable” representation of an object, for logging or debugging purposes.

As for math operations, JavaScript doesn’t provide a way to “override” them using methods, so real life projects rarely use them on objects.

<a href="/symbol" class="page__nav page__nav_prev"><span class="page__nav-text"><span class="page__nav-text-shortcut"></span></span><span class="page__nav-text-alternate">Previous lesson</span></a><a href="/data-types" class="page__nav page__nav_next"><span class="page__nav-text"><span class="page__nav-text-shortcut"></span></span><span class="page__nav-text-alternate">Next lesson</span></a>

<span class="share-icons__title">Share</span><a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Fobject-toprimitive" class="share share_tw"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Fobject-toprimitive" class="share share_fb"></a>

<a href="/tutorial/map" class="map"><span class="map__text">Tutorial map</span></a>

## <a href="#comments" id="comments">Comments</a>

<span class="comments__read-before-link">read this before commenting…</span>

- If you have suggestions what to improve - please [submit a GitHub issue](https://github.com/javascript-tutorial/en.javascript.info/issues/new) or a pull request instead of commenting.
- If you can't understand something in the article – please elaborate.
- To insert few words of code, use the `<code>` tag, for several lines – wrap them in `<pre>` tag, for more than 10 lines – use a sandbox ([plnkr](https://plnkr.co/edit/?p=preview), [jsbin](https://jsbin.com), [codepen](http://codepen.io)…)

<a href="/tutorial/map" class="map"></a>

#### Chapter

- <a href="/object-basics" class="sidebar__link">Objects: the basics</a>

#### Lesson navigation

- <a href="#conversion-rules" class="sidebar__link">Conversion rules</a>
- <a href="#symbol-toprimitive" class="sidebar__link">Symbol.toPrimitive</a>
- <a href="#tostring-valueof" class="sidebar__link">toString/valueOf</a>
- <a href="#further-conversions" class="sidebar__link">Further conversions</a>
- <a href="#summary" class="sidebar__link">Summary</a>

- <a href="#comments" class="sidebar__link">Comments</a>

Share

<a href="https://twitter.com/share?url=https%3A%2F%2Fjavascript.info%2Fobject-toprimitive" class="share share_tw sidebar__share"></a><a href="https://www.facebook.com/sharer/sharer.php?s=100&amp;p%5Burl%5D=https%3A%2F%2Fjavascript.info%2Fobject-toprimitive" class="share share_fb sidebar__share"></a>

<a href="https://github.com/javascript-tutorial/en.javascript.info/blob/master/1-js/04-object-basics/09-object-toprimitive" class="sidebar__link">Edit on GitHub</a>

- © 2007—2021  Ilya Kantor
- <a href="/about" class="page-footer__link">about the project</a>
- <a href="/about#contact-us" class="page-footer__link">contact us</a>
- <a href="/terms" class="page-footer__link">terms of usage</a>
- <a href="/privacy" class="page-footer__link">privacy policy</a>
