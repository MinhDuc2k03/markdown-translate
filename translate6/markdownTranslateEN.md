# Avoiding empty() in PHP
The language construct `empty()` appears rather versatile. It's like a Swiss army knife with a thousand blades, ready to hurt you if you grab it by the wrong end. Or a jack of all trades, master of none. Most of all, `empty()` is a poor communicator.

I spot `empty()` in poorly-aged closed-source projects. I discover `empty()` in brand-new closed-source projects from last week. And I meet `empty()` again in open-source projects with millions of downloads.

So what is the problem with `empty()` when so many people use it?



## Problem
If you refer to the documentation for `empty()`, you will find the following:

>*"Determine whether a variable is considered to be empty. A variable is considered empty if it does not exist or if its value equals `false`. `empty()` does not generate a warning if the variable does not exist."*
>
>[**PHP manual: empty()**](https://www.php.net/manual/en/function.empty.php)

If you ignore the internal implementation and possible performance issues, using `empty()` is identical to using `isset()` and a [**loose comparison with** `false`](https://www.php.net/manual/en/language.types.boolean.php#language.types.boolean.casting).

```php
<?php

declare(strict_types=1);

-if (empty($value)) {
+if (!isset($value) || $value == false) {
     // ...
}
```

When you already know that a variable, a property, a function or method parameter, and a function or method return value are defined, why would you use `isset()`?

When you already know that a variable, a property, a function or method parameter, and a function or method return value assume multiple types, why would you use loose comparison and not handle each acceptable type and value separately?

When you already know that a variable, a property, a function or method parameter, and a function or method return value assume a single type, why would you use loose instead of strict comparison?

Using `empty()`, `isset()`, or loose comparisons is a wishy-washy business. Is that how you want to work?

As mentioned, I often find `empty()` in legacy code bases. Some of these projects run on outdated versions of PHP and are entirely unaware of property, parameter, and return type declarations. In these projects, you often can not find DocBlocks for properties, function and method parameters, or function and method return types.

When you pick up maintenance of these projects and begin to fix bugs, update PHP versions and eventually modernize them, you have difficulty figuring out what the original authors intended to do when they used `empty()`.

Were the original authors aware of the quirks of `empty()`? Did the authors intend a property, a function, or a method parameter to accept all types and values? Did the authors plan to return all types and values from a function or method? Did the authors, who have long disappeared and ghosted the project owners, really think the use of `empty()` through?

The original author could be you. The maintainer could also be you, picking up the project after years. Perhaps you had a stroke or an accident in the meantime that impaired your cognitive abilities. Now you have difficulty understanding what the original author intended. Perhaps the authors and maintainers are entirely different persons. Maybe you are in perfect health and still struggle to understand the original author's intent.

While you write the code for the computer to process, you - as the author - also write the code for the person maintaining it after. By more or less carefully selecting language features, you instruct the computer, but you also communicate your intent to the maintainer.

> *"Any fool can write code that a computer can understand. Good programmers write code that humans can understand."*
>
> [**Martin Fowler, Refactoring**](https://martinfowler.com/books/refactoring.html)

Let's look at all cases when `empty()` returns true, and explore alternatives that better communicate your situational awareness and intent!



## Candidates
 - [**undefined variable**](##undefined-variable)
 - [**undefined instance property**](##undefined-instance-property)
 - [**undefined static property**](##undefined-static-property)
 - [**inaccessible instance property**](##inaccessible-instance-property)
 - [**inaccessible static property**](##inaccessible-static-property)
 - [**null**](##null)
 - [**array**](##array)
 - [**bool**](##boolean)
 - [**float**](##float)
 - [**int**](#int)
 - [**string**](##string)
 - [**SimpleXMLElement**](##simplexmlelement)



## Undefined variable
`empty()` returns `true` when the argument is an undefined variable.

```php
<?php

declare(strict_types=1);

var_dump(empty($value)); // (bool)true
```



## Undefined instance property
`empty()` returns `true` when the argument is an undefined instance property.

```php
<?php

declare(strict_types=1);

$object = new stdClass();

var_dump(empty($object->value)); // (bool)true
```

As a side effect, `empty()` also invokes the magic method `__isset()` when you reference an undefined instance property of an object that declares an `__isset()` method.

```php
<?php

declare(strict_types=1);

$object = new class() {
    public function __isset(string $name): bool
    {
        echo '👋';

        return true;
    }
};

var_dump(empty($object->value)); // 👋(bool)true
```



## Undefined static property
`empty()` returns `true` when the argument is an undefined `static` property.
```php
<?php

declare(strict_types=1);

$object = new stdClass();

var_dump(empty($object::$value)); // (bool)true
```



## Inaccessible instance property
`empty()` returns `true` when the argument is an inaccessible instance property.

As a side effect, `empty()` invokes the magic method `__isset()` when it exists.

```php
<?php

declare(strict_types=1);

$object = new class() {
    private $value = 9000;
    protected $otherValue = 9001;

    public function __isset(string $name): bool
    {
        echo '👋';

        return true;
    }
};

var_dump(empty($object->value)); // 👋(bool)true
var_dump(empty($object->otherValue)); // 👋(bool)true
```



## Inaccessible static property
`empty()` returns `true` when the argument is an inaccessible `static` property.

```php
<?php

declare(strict_types=1);

$object = new class() {
    private static $value = 9000;
    protected static $otherValue = 9000;
};

var_dump(empty($object::$value)); // (bool)true
var_dump(empty($object::$otherValue)); // (bool)true
```



## Null
`empty()` returns `true` when the argument is `null`.

```php
<?php

declare(strict_types=1);

$value = null;

var_dump(empty($value)); // (bool)true
```



## Array
`empty()` returns `true` when the value is an empty `array`.

```php
<?php

declare(strict_types=1);

$value = [];

var_dump(empty($value)); // (bool)true
```



## Boolean
`empty()` returns `true` when the argument is a `bool` with the value `false`.

```php
<?php

declare(strict_types=1);

$value = false;

var_dump(empty($value)); // (bool)true
```



## Float
`empty()` returns `true` when a variable is a `float` with the value `0.0` or `-0.0`.

```php
<?php

declare(strict_types=1);

$value = 0.0;

var_dump(empty($value)); // (bool)true
```



## Int
`empty()` returns `true` when the argument is an `int` with the value `0`.

```php
<?php

declare(strict_types=1);

$value = 0;

var_dump(empty($value)); // // (bool)true
```



## String
`empty()` returns `true` when the argument is a `string` with the value `''`.

```php
<?php

declare(strict_types=1);

$value = '';

var_dump(empty($value)); // // (bool)true
```

`empty()` also returns `true` when the argument is a `string` with the value `'0'`.

```php
<?php

declare(strict_types=1);

$value = '0';

var_dump(empty($value)); // // (bool)true
```



## SimpleXMLElement
`empty()` returns `true` when the argument is an instance of `SimpleXMLElement` constructed from an XML string representing an element without attributes and children.

```php
<?php

declare(strict_types=1);

$value = new SimpleXMLElement('<foo></foo>');

var_dump(empty($value)); // (bool)true
```



## Conclusion
Do not use `empty()`. Do not write code that allows a variable, a property, a parameter, or a return value to assume multiple types. Use type-safe comparisons.

