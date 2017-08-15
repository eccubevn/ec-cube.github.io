## How to use composer
- You can install / delete plugins from Composer. 
- You can activate / deactivate the plugin from the administration screen.

### Added EC-CUBE repository

Have 2 methods:

M1. Use command line:
```
$ php composer.phar config repositories.eccube composer https://repo.ec-cube.net
$ php composer.phar config repositories.eccube.options '{
    "http": {
        "header": [
            "X-ECCUBE-KEY: <認証キー>",
            "X-ECCUBE-URL: <サイトURL>",
            "X-ECCUBE-VERSION: <EC-CUBEバージョン>"
        ]
    }
}'
```

M2. Use composer.json:

```
"repositories": {
    "eccube": {
        "type": "composer",
        "url": "https://alpha-repo.ec-cube.net",
        "options": {
            "http": {
                "header": ["X-ECCUBE-KEY: 3f28c8fdea1a47734e9c2dc6f910abbec32db08f", "X-ECCUBE-URL: http://localhost", "X-ECCUBE-VERSION: 3.0.13"]
            }
        }
    }
}
```
### Installation

Run command line: `$ php composer.phar require ec-cube/Maker`.

### Delete

`$ php composer.phar remove ec-cube/Maker`

### Supported

At the moment (2017/02 / 24) the following official plug-in is available from the repository.

name | package name | version
-- | -- | --
Category Content | Ec-cube / CategoryContent | 1.0.0
coupon | Ec-cube / Coupon | 2.0.0
EC-CUBE Web API | Ec-cube / EccubeApi | 1.0.3
Listing advertisement | Ec-cube / ListingAdCsv | 1.0.1
Mail magazine management | Ec-cube / MailMagazine | 1.0.0
Mail settings | Ec-cube / MailTemplateEditor | 1.0.0
Maker management | Ec-cube / Maker | 1.0.0
point | Ec-cube / Point | 1.0.0
Recommended order registration | Ec-cube / ProductPriority | 1.0.0
Product review | Ec-cube / ProductReview | 1.0.0
Featured Products | Ec-cube / Recommend | 2.0.0
Related products | Ec-cube / RelatedProduct | 1.0.0
Sales summary | Ec-cube / SalesReport | 1.0.0

In addition, the following plugins are registered in the repository for testing dependency between plugins.

package name | version | Remarks
-- | -- | --
Ec-cube / Foo | 1.0.0 |  
Ec-cube / Bar | 1.0.0 | Packages that depend on ec-cube / Foo

### Note: 
Test server: alpha-repo.ec-cube.net

### Plugin Setting

Minimum setting:
```
{
    "name": "ec-cube/Maker",
    "version": "1.0.0",
    "type": "eccube-plugin",
    "require": {
        "ec-cube/plugin-installer": "~1.0"
    },
    "extra": {
        "code": "Maker"
    }
}
```
Note:

item name | value
-- | --
Name | "ec-cubbe/プラグインコード"
Version | Plugin version
Type | "eccube-plugin"
Require | "ec-cube/plugin-installer": "0.0.1"Be sure to rely on. If you have other packages you want to use, please add it.
Extra | "code": "プラグインコード"
