![Preview of Kirby blurry placeholder plugin](./.github/social-preview.png)

# Kirby Blurry Placeholder

This plugin implements progressive image loading, proving a better user experience. Tiny thumbnails which inherit the aspect ratio of their source image are combined with a blurry effect for a better placeholder than a solid colour, without sacrificing payload.

1. An inline, URI-encoded SVG will fill the `src` attribute of a given image element when the HTML is requested. The blurred image is wrapped in a SVG to avoid rasterizing the filter.
2. The large images are then only requests when they are within the viewport.

## Key Features

- ✨ Avoids content jumping (keeping aspect ratio)
- 🖼 Available as Kirbytag
- 🗃 Extends `Kirby\Cms\File` methods
- 🔍 SEO-friendly
- ⚡️ Vanilla JavaScript lazy loader included

## Requirements

- Kirby 3
- PHP 7.4+

## Installation

### Download

Download and copy this repository to `/site/plugins/kirby-blurry-placeholder`.

### Git submodule

```
git submodule add https://github.com/johannschopplich/kirby-blurry-placeholder.git site/plugins/kirby-blurry-placeholder
```

### Composer

```
composer require johannschopplich/kirby-blurry-placeholder
```

## Usage

### … As `(blurryimage: …)` Kirbytag

This plugin doesn't extend the core `(image: …)` Kirbytag, but builds upon it. So all of the options present are available in the plugin's Kirbytag as well.

The `(blurryimage: …)` tag:
- Encodes a blurry image placeholder as URI in the `src` attribute.
- Sets the original image's URL as `data-src` or a set of responsive images as `data-srcset`.
- Adds a `data-lazyload` attribute for selection by the lazy loading library.

Example use within a KirbyText field:
```
(blurryimage: myimage.jpg)
(blurryimage: myimage.jpg link: https://example.com)
(blurryimage: myimage.jpg class: is-poster)
```

If you have enabled `srcset`'s in the options, the Kirbytag syntax stays the same. Just the output changes.

### … As File Method

`$file->placeholderUri()` creates and returns the URI-encoded SVG placeholder.

```html
// Using the `placeholderUri` for an inlined image in the `src` attribute
<img src="<?= $image->placeholderUri() ?>" data-src="<?= $image->url() ?>" data-lazyload alt="<?= $image->alt() ?>">
```

## Lazy Loading in the Frontend

You have two options to lazily load the larger image.

### Use the Included Lazyload Hook

```js
import { useLazyload } from './src/useLazyload'

const observer = useLazyload()
observer.observe()
```

You may inspect the source to gain more information about options. In a nutshell, it's a SEO-friendly and modernized derivate of [lozad.js](https://github.com/ApoorvSaxena/lozad.js).

**Automatically setting the sizes attribute**

useLazyload supports setting the `sizes` attribute automatically, corresponding to the current size of your image. For this to work, the `data-sizes` attribute has to be set to `auto`. If you have `srcset`'s enabled in your configuration, this is already done for you when using the `(blurryimage: …)` Kirbytag.

### Use a Lazy Loader of Your Choice

Each parsed Kirbytag adds the `data-lazyload` attribute to the `img` element.

Thus you can add the `[data-lazyload]` selector to you lazy loader.

> Note: A `.lazyload` class is intentionally not added to avoid naming conflicts. I also prefer data attributes over classes for selectors only used by JavaScript manipulation. 🤷‍♂️

## Options

> All options are namespaced under `kirby-extended.blurry-placeholder`.

| Option | Default | Description |
| --- | --- | --- |
| `pixel-target` | `60` | Aim for a placeholder image of ~P pixels (w * h = ~P).
| `srcset.enable` | `false` | Boolean indicating if responsive images should be created when using the Kirbytag.
| `srcset.preset` | `null` | A preset passed to Kirby's `srcset` method when using the Kirbytag.
| `srcset.sizes` | `auto` | String for the `data-sizes` attribute when using the Kirbytag.

For example in your `config.php`:

```php
<?php

return [
    // …
    'kirby-extended.blurry-placeholder' => [
        'srcset' => [
            'enable' => true,
            'preset' => 'text'
        ]
    ]
]
```

## Placeholders in Action

> Note: Slowed down so that you can see better how placeholders look.

![GIF showing plugin in action](./.github/kirby-blurry-placeholder-preview.gif)

## TODO

- [ ] Add tests

## Credits

- AMP's [blurry image implementation](https://github.com/ampproject/amp-toolbox/blob/0c8755016ae825b11b63b98be83271fd14cc0486/packages/optimizer/lib/transformers/AddBlurryImagePlaceholders.js)
- [lozad.js](https://github.com/ApoorvSaxena/lozad.js)

## License

[MIT](https://opensource.org/licenses/MIT)
