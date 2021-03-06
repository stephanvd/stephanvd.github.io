---
layout: post
title: "Keep Nested Resource Routes Succint With The Shallow Option"
published: true
excerpt_separator: <!--more-->
---

I just got my first PR merged into Laravel! It adds the option for `shallow` resource routes. You can see the PR [here](https://github.com/laravel/framework/pull/31208).

Without any prior knowledge of Laravel internals this feature took me about an hour to develop. Props to the Laravel maintainers for an easy to navigate codebase and quick PR resolution!

### Shallow Nesting

It's a good practice not to nest resources too deep. Routes and their url helpers quickly get unwieldy. Shallow nesting is a way to prevent deep nesting of resources. Collection actions (index, new, create) are scoped to the parent to provide context, while member actions (show, edit, update, destroy) are placed in the root or namespace when provided. This strikes a nice balance between being descriptive and being compact. An additional advantage is that records will have a single canonical route.

<!--more-->

To achieve this we needed to configure our routes like:
```php
Route::resource('foo.bar', 'BarController')->only(['index', 'create', 'store']);
Route::resource('bar', 'BarController')->only(['show', 'edit', 'update', 'destroy']);
```

With the `shallow` option we can simply type:
```php
Route::resource('foo.bar', 'BarController')->shallow();
```

And have the following routes generated for us:

```
| PUT|PATCH | bar/{bar}            | bar.update     | App\Http\Controllers\BarController@update  | web,auth |
| DELETE    | bar/{bar}            | bar.destroy    | App\Http\Controllers\BarController@destroy | web,auth |
| GET|HEAD  | bar/{bar}            | bar.show       | App\Http\Controllers\BarController@show    | web,auth |
| GET|HEAD  | bar/{bar}/edit       | bar.edit       | App\Http\Controllers\BarController@edit    | web,auth |
| POST      | foo/{foo}/bar        | foo.bar.store  | App\Http\Controllers\BarController@store   | web,auth |
| GET|HEAD  | foo/{foo}/bar        | foo.bar.index  | App\Http\Controllers\BarController@index   | web,auth |
| GET|HEAD  | foo/{foo}/bar/create | foo.bar.create | App\Http\Controllers\BarController@create  | web,auth |
```

Expect this feature to land in the next release of Laravel 6.x!

**Update:** This has now been released in [Laravel 6.13.0](https://laravel-news.com/laravel-6-13-0)!
