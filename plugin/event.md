---
layout: default
title: イベント
---

# {{ page.title }}

プラグインを作成する時に理解する内容の一つにイベントがあります。

Một trong những nội dung lý giải khi tạo Plugin là Event.

このイベントはSymfony2のイベントと同義の意味なのですが、EC-CUBE3ではSymfony2のイベントをラップしたフックポイントというものを3.0.9から用意しています。

Tuy đây là event có ý nghĩa giống với event của Symfony2 nhưng trong EC-CUBE 3, từ 3.0.9 trở đi đã chuẩn bị Hookpoint có thể wrap event của Symfony2.

### イベント定義 (Định nghĩa Event)

プラグインでイベントを利用する場合、`config.yml`と`event.yml`に定義する必要があります。

Khi sử dụng event trong plugin, cần phải định nghĩa cho `config.yml`và `event.yml`

- `config.yml`にイベントクラスを定義

Định nghĩa event class cho `config.yml`

```
event:
    - XXXXEvent
```

- `event.yml`にイベントを定義

Định nghĩa event cho `event.yml`

```
eccube.event.app.request:
  - [onAppRequest, NORMAL]

eccube.event.route.homepage.controller:
  - [onRouteHomepageController, NORMAL]

front.cart.index.initialize:
    - [onFrontCartIndexInitialize, NORMAL]

Shopping/index.twig:
    - [onShoppingIndexRender, NORMAL]
```

イベントクラス、event.ymlは下記に配置します。

event.yml được sắp đặt như dưới đây.

```
[プラグインコード]
  ├── XXXXEvent.php
  └── event.yml
```

イベントクラスには、event.ymlに定義したイベント名の関数を記述します。

Trong event class có mô tả hàm số của tên event đã được định nghĩa cho event.yml

```php
<?php

namespace Plugin\XXXX;

use Eccube\Application;
use Eccube\Event\EventArgs;
use Eccube\Event\TemplateEvent;
use Symfony\Component\HttpKernel\Event\FilterControllerEvent;
use Symfony\Component\HttpKernel\Event\GetResponseEvent;

class XXXXEvent
{

    private $app;

    public function __construct(Application $app)
    {
        $this->app = $app;
    }

    public function onAppRequest(GetResponseEvent $event)
    {

    }

    public function onRouteHomepageController(FilterControllerEvent $event)
    {

    }

    public function onFrontCartIndexInitialize(EventArgs $event)
    {

    }

    public function onShoppingIndexRender(TemplateEvent $event)
    {

    }

}
```


### 本体のバージョンチェック (Check version của Hontai)

本体のバージョンによりサポートするイベントが異なります。その場合、EC-CUBEのバージョンチェックを行い、イベントを2回実行させないようにします。

Tùy theo version của hontai mà event support sẽ khác nhau. Trong trường hợp đó, ta tiến hành check version của EC-CUBE để không phải chạy event 2 lần

- `event.yml`にイベントを定義

Định nghĩa event cho `event.yml`

```
Shopping/index.twig:
    - [onShoppingIndexRender, NORMAL]

eccube.event.render.product_detail.before:
    - [onRenderProductDetailBefore, NORMAL]
```

- イベントクラス

Event class

```php
<?php

namespace Plugin\XXXX;

use Eccube\Application;
use Eccube\Common\Constant;
use Eccube\Event\TemplateEvent;
use Symfony\Component\HttpKernel\Event\FilterResponseEvent;

class XXXXEvent
{

    private $app;

    public function __construct(Application $app)
    {
        $this->app = $app;
    }

    public function onShoppingIndexRender(TemplateEvent $event)
    {

    }

    public function onRenderProductDetailBefore(FilterResponseEvent $event)
    {
        if ($this->isSupportVersion() {
            return;
        }

        // add code
    }

    /**
     * 本体のバージョンチェック
     * 指定されたバージョン以上かチェック
     *
     * @param string $version
     * @return mixed
     */
    private function isSupportVersion($version = '3.0.9')
    {
        return version_compare(Constant::VERSION, $version, '>=');
    }
}
```

※バージョン毎にサポートする必要がなければバージョンチェックは不要です。

Nếu không cần support cho mỗi version thì không cần check version nữa.


### イベントの肥大化を防ぐ (Phòng tránh phình to event)

設定するイベントが増えると当然イベントクラスの中身が肥大化してきます。その場合、イベント毎にクラスを分割して管理する方法をお勧めします。

Khi số event cài đặt tăng lên thì đương nhiên bên trong event class sẽ phình to ra. Trong trường hợp đó, tôi khuyên nên chia class cho từng plugin để quản lý.

- イベント用クラスを作成

Tạo class dùng cho event

```
[プラグインコード]
  ├── Event
  │   ├── XXXXEvent.php
  │   └── YYYYEvent.php
````


- ServiceProviderにイベントクラスを定義

Định nghĩa event class cho ServiceProvider

```php
// Event
$app['[プラグインコード].event.xxxx'] = $app->share(function () use ($app) {
    return new XXXXEvent($app);
});

$app['[プラグインコード].event.yyyy'] = $app->share(function () use ($app) {
    return new YYYYEvent($app);
});
```

- Eventクラスの作成

Tạo Event class
```php
<?php

namespace Plugin\XXXX;

use Eccube\Application;
use Eccube\Common\Constant;
use Eccube\Event\TemplateEvent;
use Symfony\Component\HttpKernel\Event\FilterResponseEvent;

class XXXXEvent
{

    private $app;

    public function __construct(Application $app)
    {
        $this->app = $app;
    }

    public function onShoppingIndexRender(TemplateEvent $event)
    {
        $this->app['xxxx.event.xxxx']->onShoppingIndexRender($event);

    }

    public function onRenderProductDetailBefore(FilterResponseEvent $event)
    {
        if ($this->isSupportVersion() {
            return;
        }

        $this->app['xxxx.event.yyyy']->onRenderProductDetailBefore($event);
    }

    /**
     * 本体のバージョンチェック
     * 指定されたバージョン以上かチェック
     *
     * @param string $version
     * @return mixed
     */
    private function isSupportVersion($version = '3.0.9')
    {
        return version_compare(Constant::VERSION, $version, '>=');
    }
}
```


- XXXXEvent

```php
<?php

namespace Plugin\XXXX\Event;

use Eccube\Application;
use Eccube\Common\Constant;
use Eccube\Event\TemplateEvent;

class XXXXEvent
{

    private $app;

    public function __construct(Application $app)
    {
        $this->app = $app;
    }

    public function onShoppingIndexRender(TemplateEvent $event)
    {
        // add code
    }
}
```


- YYYYEvent

```php
<?php

namespace Plugin\XXXX\Event;

use Eccube\Application;
use Eccube\Common\Constant;
use Symfony\Component\HttpKernel\Event\FilterResponseEvent;

class YYYYEvent
{

    private $app;

    public function __construct(Application $app)
    {
        $this->app = $app;
    }

    public function onRenderProductDetailBefore(FilterResponseEvent $event)
    {
        // add code
    }
}
```

作成するイベントクラスは業務に応じて分割してください。

Hãy chia những Event class được tạo ứng với business.
