---
title: Number Helper
description: Number Helper Guide for the OriginPHP Framework
extends: _layouts.documentation
section: content
---
# Number Helper

The number helper provides a number of useful functions, you can configure the defaults in your `AppController` like this:

```php
use Origin\Utility\Number;
public function initialize(){
    Number::locale([
        'currency'=>'USD', // default currency
        'thousands'=>',',
        'decimals'=>'.',
        'places'=>2
        ]);
}
```

Once you have this configured whenever you use the number helper it will format based upon those defaults
unless you tell it otherwise.

## Formating numbers

```php
$this->Number->format('123456789.123456789'); // 123,456,789.12
$this->Number->format('123456789.123456789',['places'=>4]); //123,456,789.1235
```

Options for the number formatting are these, currency, decimal and percentage use the number formatter so you can also pass these options to those methods as well.

- before: Adds text before the string
- after: adds text after the string
- thousands: the thousands separator
- decimals: the decimals separator
- places: how many decimal points to show

## Formating Currencies

To format a currency you can do it like this, if you don't supply a currency as the second argument then
it will use your default currency.

```php
echo $this->Number->currency('1000000'); // $1,000,000.00
echo $this->Number->currency('1000000','GBP'); //£1,000,000.00
echo $this->Number->currency('1000','USD',['places'=>'per group']);
```

By default the number helper can work with USD, GBP, EUR, CAD, AUD,CHF AND JPY out of the box. But you can also add your own currencies.

```php
public function initialize(){
    Number::addCurrency('CNY',['before'=>'¥','name'=>'Chinese Yuan']);
}
```

Then from your view just supply the currency code as the second argument.

```php
echo $this->Number->currency('1000000', 'CNY'); // ¥1,000,000.00
```

## Formating Decimals

```php
echo $this->Number->decimal('100'); // 100.00
echo $this->Number->decimal('100.1',3); // 100.100
```

## Formating Percentages

On top the number options, when formating percentages there is an additional option which is `multiply`, this will multiply the result, this is handy when you have the result in decimal format.

```php
echo $this->Number->percent(50); // 50.00%
echo $this->Number->percent(0.3333333333, 2, ['multiply'=>true]);// 33.33%
```

## Parsing

The number formatter assumes that the numbers are do not have a thousands separator and the decimal point is
`.`. You can delocalize the user inputted data in the middleware, the controller or model. The simplest way is to use the model callbacks such as `beforeValidate` or `beforeSave`.

```php
use Origin\Utility\Number;
$user->balance = Number::parse($user->balance); //1.000,23 -> 1000.23
```