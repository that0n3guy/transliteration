Transliteration - Laravel 4 text cleaning Package
=====================

Transliteration provides one-way string transliteration (romanization) and cleans text by replacing unwanted characters.

> ...it takes Unicode text and tries to represent it in US-ASCII characters (universally displayable, unaccented characters) by attempting to transliterate the pronunciation expressed by the text in some other writing system to Roman letters.

This adapts the module from https://drupal.org/project/transliteration for use with Laravel.

## Features

* Transliterate text to US-ASCII characters

## Why use this?

* I use this for filename on uploads.   See this image:


![](https://drupal.org/files/styles/grid-3/public/images/translit_0.png?itok=CwKAPBtB)*

* I use this with [Andrew Elkins's Cabinet](https://github.com/andrewelkins/cabinet)

## Quick start

### Required setup

In the `require` key of `composer.json` file add the following

#### Laravel 5

    "that0n3guy/transliteration": "2.0.x"

#### Laravel 4

    "that0n3guy/transliteration": "1.0.x"

Run the Composer update command

    $ composer update

In your `config/app.php` add `'That0n3guy\Transliteration\TransliterationServiceProvider'` to the end of the `$providers` array

```php
'providers' => array(

    'Illuminate\Foundation\Providers\ArtisanServiceProvider',
    'Illuminate\Auth\AuthServiceProvider',
    ...
    'That0n3guy\Transliteration\TransliterationServiceProvider',

),
```

### How to use

Simply call the Transliteration class:

```php
Route::get('/test', function(){
  echo Transliteration::clean_filename('test& ® is true');
});
```

This would return `test_r_is_true`

### Set a language

You can optionally set a [Optional ISO 639 language code](http://en.wikipedia.org/wiki/List_of_ISO_639-1_codes).  Do it like so:

```php
Route::get('/test', function(){
  echo Transliteration::clean_filename('testing Japanese 日本語', 'jpn');
});
```

This would return `testing_Japanese_Ri_Ben_Yu_`.


## How to use with [Cabinet](https://github.com/andrewelkins/cabinet)


Add something like:

```php
// if using transliteration
if (class_exists( 'That0n3guy\Transliteration\Transliteration' )) {
  $file->fileSystemName = Transliteration::clean_filename($file->getClientOriginalName());
}
```

To your Upload controller.  For example.  I added it to my UploadController.php and my store() method looks like so:

```php
/**
 * Stores new upload
 *
 */
public function store()
{
    $file = Input::file('file');

    // if using transliteration
    if (class_exists( 'That0n3guy\Transliteration\Transliteration' )) {
      $file->fileSystemName = Transliteration::clean_filename($file->getClientOriginalName());
    }

    $upload = new Upload;

    try {
        $upload->process($file);
    } catch(Exception $exception){
        // Something went wrong. Log it.
        Log::error($exception);
        $error = array(
            'name' => $file->getClientOriginalName(),
            'size' => $file->getSize(),
            'error' => $exception->getMessage(),
        );
        // Return error
        return Response::json($error, 400);
    }

    // If it now has an id, it should have been successful.
    if ( $upload->id ) {
      ...
```
