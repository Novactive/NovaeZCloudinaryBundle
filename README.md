# Novactive eZ Cloudinary Bundle

----

This repository is what we call a "subtree split": a read-only copy of one directory of the main repository. 
It is used by Composer to allow developers to depend on specific bundles.

If you want to report or contribute, you should instead open your issue on the main repository: https://github.com/Novactive/Nova-eZPlatform-Bundles

Documentation is available in this repository via `.md` files but also packaged here: https://novactive.github.io/Nova-eZPlatform-Bundles/master/CloudinaryBundle/README.md.html

----

Novactive eZ Cloudinary Bundle is an eZPlatform bundle for Images optimizations and manipulations.

This bundle brings the power of [Cloudinary](https://demo.cloudinary.com/?mode=default) in your eZ Platform project.

For this first version, the plugin allows you to define Cloudinary Variation on top of eZ Variations.
Images stay on your servers but the SRC is adapted to make Cloudinary rendering/manipulating images.

> All the configuration is SiteAccessAware then you can have different one depending on the SiteAccess

## INSTALL

### Use Composer

Add the following to your composer.json and run `composer require novactive/ezcloudinarybundle` to refresh dependencies:

```json
# composer.json

"require": {
    "novactive/ezcloudinarybundle": "dev-master",
}
```


### Register the bundle

Activate the bundle in `app\AppKernel.php` file.

```php
// app\AppKernel.php

public function registerBundles()
{
   ...
   $bundles = array(
       new FrameworkBundle(),
       ...
       new Novactive\Bundle\eZCloudinaryBundle\NovaeZCloudinaryBundle(),
   );
   ...
}
```

### Setup your credentials

```yaml
nova_ezcloudinary:
    authentification:
        cloud_name: 'demo'
        api_key: "xxxxx"
        api_secret: "xxxx"
```

## Usage

This bundle mimics the native image variation system.

```yaml
parameters:
    nova_ezcloudinary.default.cloudinary_variations:
            simpletest1:
                ezreference_variation: 'Native eZ Variation Name, ~ means original'
                filters: # look at the documentation on Cloudinary
                    width: 200
                    height: 200
                    gravity: 'face'
                    radius: 'max'
                    effect: 'sepia'
            simpletest2: # look at the documentation on Cloudinary
                ezreference_variation: 'medium' # Cloudinary manipulation are going to be base on the medium alias
                filters: # look at the documentation on Cloudinary
                    transformation:
                        width: 300
                        height: 300
                        effect: 'sepia'
                        radius: 'max'
                        fetch_format: 'auto'
                        angle: 45

```

In your template

```twig
    {{ ez_render_field( content, "image",{
        "parameters": {"alias": 'simpletest2'},
        "attr" : { "class" : "img-responsive" }
    }
    ) }}
```

Automatically, `nova_ezcloudinary_alias` will be used instead of `ez_image_alias`.
The bundle fallback on the native Variation system if the alias name does not exist in `cloudinary_variations`

Then basically there is no change in your code, just yaml configuration for your Variation.

> if you have override the content_fields, be sure to update the call to `nova_ezcloudinary_alias`


### Chained Transformations
Chained transformations can be done by adding an array to the configuration like so:
```yaml 
filters:
  transformation:
    - secure: 'true'
      crop: 'crop'
      height: 823
      width: 1920
      quality: 'auto:best'
    - crop: 'crop'
      y: 0
      x: 415
      width: 1234
      height: 823
      quality: 'auto:best'
    - crop: 'scale'
      width: 537
      height: 358
      aspect_ratio: '3:2'
      quality: 'auto:eco'
```

## Local mode

This bundle uses the fetch mode of Cloudinary, then images have to be "reachable" to be converted.
BUT, obviously your localhost is not public, to bypass that situation, we recommand the usage of `ngrok`

Let's assume your webserver listen on the TCP port 42080
```bash
$ ngrok http 42080
```

You will get something like:  `http://xxxxx.ngrok.io`
And you can set up that in the configuration:

```yaml
parameters:
    nova_ezcloudinary.default.cloudinary_fecth_proxy:
        host: xxxxx.ngrok.io
```

> you can do your own tunnel, here, but `ngrok` is really good at it

## Local mode without Cloudinary

You can also disable all Cloudinary variations for you local usages, using only native ezplatform variation:

```yaml
parameters:
    nova_ezcloudinary.default.cloudinary_disabled: true
    nova_ezcloudinary.default.cloudinary_fallback_variation: original # Will replace all unknown variation by original
```


License
-------

[License](LICENSE)
