# BW Javascript SDK Bundle
This bundle provides twig functions to easily add common Javascript SDK tags to any page.

It has been structured to be easily expanded for whatever SDKs you may want. Out of the box this bundle will support Google Analytics, Woopra, Facebook, Facebook Pixel and Twitter.

This bundle is only configured and tested to be used for Symfony >=3.4 using the new Flex file structure.

## Installation
This bundle will be submitted to the Symfony Flex contrib repository shortly. Until then you'll have to add the bundle into your bundles.php file manually.
```bash
composer req silverbackis/js-sdk-bundle
```

```php
// config/bundles.php
return [
    JsSdkBundle\JsSdkBundle::class => ['all' => true],
];
```

## Getting Started
Javascript blocks can be configured directly from your twig templates using the following functions
```twig
{{ js_sdk_add_block(sdk_name, sdk_block_name, at_sdk_block_name, before_at_sdk_block_name, override_params) }}
{{ js_sdk_output(sdk_name, override_params) }}
```

You can duplicate an SDK block in its current state at any point in the Twig template
```twig
{{ js_sdk_duplicate(sdk_name, new_sdk_name, override_params) }}
```

You can also remove a block (if you've duplicated a block but want to remove a specific section for example)
```twig
{{ js_sdk_remove_block(sdk_name, sdk_block_name) }}
```

## Google Analytics Example
The first SDK implemented is for Google Analytics. Here is an example of how you may end up using the functions.

This will state that when you output the google_analytics sdk, you want to include the page_view block:
```twig
{{ js_sdk_add_block('google_analytics', 'page_view') }}
```

This will state that you also want the extended ecommerce initialised, but you want this BEFORE the 'page_view' block - you could just define this first, but in some situations that may not be as easy. If the final parameter is false or ommitted, the ec/init block would be inserted after the page_view block:
```twig
{{ js_sdk_add_block('google_analytics', 'ec/init', 'page_view', true) }}
```

This will output the sdk block javascript with all the options above - this should always be executed last in your twig template. You may wish to include an empty block in your base twig template that you can insert this into from child pages when ready, or that you can override. E.g. the base twig template could always output a page_view but on pages where there are product impressions you could override this to also include additional tracking code. With analytics it is beneficial to add ec tracking code before the initial page view otherwise you'll need to send an additional event to track data:
```twig
{{ js_sdk_output('google_analytics') }}
```

This library is designed to be as flexible as possible. At any point you are able to duplicate an SDK block with a new name and new parameters. If you want to duplicate the entire tracking code for another account you could add this once you've populated all your blocks:
```twig
{{ js_sdk_duplicate('google_analytics', 'analytics_two', {tracking_function: 'ga_extra', id: 'UA-98765432'}) }}
{{ js_sdk_output('analytics_two') }}
```

Each parameter for an SDK can be configured in your *js_sdk.yaml* file. This is an example for Google Analytics. Not all parameters are required. You will need to set `enabled` to `true` for any SDK you want to use.
```yaml
# config/packages/js_sdk.yaml
js_sdk:
    google_analytics:
        enabled: true
        id: UA-12452352
        currency: USD
```

## SDK Names, Blocks and Configuration Parameters
Some parameters are common across all SDKs
| Parameter | Default | Details |
| --- | --- | --- |
| enabled | false | Enable the SDK |
| default_blocks | false | Can be an array of blocks you want. You can also include parameters if you want |

```yaml
js_sdk:
    sdk_name:
        enabled: true
        default_blocks:
            block_name: ~
            "another/block_name":
                param1: value1
                param2: value2
```

### Google Analytics
SDK name: **google_analytics**

| Parameter | Default | Details |
| --- | --- | --- |
| id | n/a (required) | Used in js_sdk_output function to initialise tracking code. E.g. 'UA-12345678' |
| debug | false | Can enable debug mode on the analytics tracking code |
| tracking_function | "ga" | The function variable to be used for tracking |
| currency | "GBP" | Used in extended e-commerce tracking to define the default currency you are recording monetary values with |

| Block | Description | Variables Used |
| --- | --- | --- |
| page_view | page view tracking code | tracking_function |
| ec/init | extended e-commerce tracking initialisation | tracking_function<br>currency |
