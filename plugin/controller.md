---
layout: default
title: コントローラー
---

# {{ page.title }}

プラグインでもコントローラーを作成することで新たな画面を作成することが可能です。作成方法は基本的には本体側と同じです。

Ngay cả Plugin cũng có thể tạo màn hình mới bằng cách tạo controller.Về cách tạo, cơ bản giống với bên Hontai (Main).

### コントローラーのルーティング定義 (Routing definition of controller)

本体側ではルーティング定義は`FrontControllerProvider`や`AdminControllerProvider`に記述しますが、プラグインでは`ServiceProvider`に定義します。 

Định nghĩa routing bên Hontai có ghi là`FrontControllerProvider`và `AdminControllerProvider` nhưng ở Plugin chỉ định nghĩa bằng `ServiceProvider` 
記述する内容は本体側と同じ内容となります。

Nội dung miêu tả sẽ giống với bên Hontai

```php
<?php

class XXXXServiceProvider implements ServiceProviderInterface
{

    public function register(BaseApplication $app)
    {
         // 管理画面定義
        $admin = $app['controllers_factory'];
        // 強制SSL
        if ($app['config']['force_ssl'] == Constant::ENABLED) {
            $admin->requireHttps();
        }

        // プラグイン用設定画面
        $admin->match('/[URL]', 'Plugin\[プラグインコード]\Controller\ConfigController::index')->bind('plugin_[プラグインコード]_config');

        $app->mount('/'.trim($app['config']['admin_route'], '/').'/', $admin);

        // フロント画面定義
        $front = $app['controllers_factory'];
        // 強制SSL
        if ($app['config']['force_ssl'] == Constant::ENABLED) {
            $front->requireHttps();
        }

        $front->match('/[URL]', 'Plugin\[プラグインコード]\Controller\XXXX::index')->bind('[プラグインコード]_xxxx');
        $front->match('/[URL]', 'Plugin\[プラグインコード]\Controller\YYYY::index')->bind('[プラグインコード]_yyyy');

        $app->mount('', $front);
```
ルーティングを作成する時の注意点として、

Những điểm chú ý khi tạo controller

- 管理画面のルーティング定義をする場合、必ず`'/'.$app['config']['admin_route']`を記述
- ->Trường hợp định nghĩa routing của Màn hình quản lý, nhất định phải mô tả '/'.$app['config']['admin_route']`
- URLやバインド名は必ずユニーク 
- ->URL và bind name phải là duy nhất
→基本的にURLやバインド名はプラグインコードをつけておけばユニークになります。(Cơ bản chỉ cần thêm plugin code vào URL và bind name là chúng sẽ trở thành duy nhất)
- プラグインの設定画面を定義する場合、

Trường hợp định nghĩa màn hình setting của Plugin
プラグインの設定画面を定義する場合、

- ルーティング定義   (Định nghĩa routing)
$app->match('/plugin/[プラグインコード]/config',

- コントローラー   (Controller)
Plugin/[プラグインコード]/Controller/ConfigController  

- バインド名   (Bind name)
->bind('plugin_[プラグインコード]_config');

- 設定画面の定義 (Định nghĩa màn hình setting)

```
$admin->match('/plugin/[プラグインコード]/config', 'Plugin\[プラグインコード]\Controller\ConfigController::index')->bind('plugin_[プラグインコード]_config');
```

Chi tiết hãy tham khảo 「プラグインの設定画面の作成方法」 của [プラグイン仕様書](http://downloads.ec-cube.net/src/manual/v3/plugin.pdf){:target="_blank"}

### 画面を表示する必要がないコントローラーを作成する場合 (Trường hợp tạo các Controller không cần hiển thị lên màn hình)

プラグインによっては画面を表示する必要がない処理の時でも必ずResponsを返すようにしてください。

Mặc dù tùy vào Plugin mà có lúc xử lý không cần thiết hiển thị lên màn hình nhưng nhất định phải làm cho có thể trả lại Respons
- **NG** : 下記のような実装では、`exit`で終了するため、EC-CUBE側に処理が戻りません。

 **NG**: Trong nội dung coding dưới đây, vì kết thúc bằng`exit` nên xử lý không quay lại phía EC-CUBE

```php
<?php

namespace Plugin\XXXX\Controller;

use Eccube\Application;
use Symfony\Component\HttpFoundation\Request;

class TopController extends AbstractController
{

    public function index(Application $app, Request $request)
    {

        if ($form->isSubmitted() && $form->isValid()) {
            // 処理...
        }

        exit;

    }

}
```

- **OK** ： Responsオブジェクトを生成し必ずResponseをreturnしてください。

**OK** Tạo object Respons và nhất định phải return Response
```php
<?php

namespace Plugin\XXXX\Controller;

use Eccube\Application;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TopController extends AbstractController
{

    public function index(Application $app, Request $request)
    {

        if ($form->isSubmitted() && $form->isValid()) {
            // 処理...
        }

        return new Response(
            '返したい文字列(何も返す必要がなければ空文字)',
            Response::HTTP_OK,
            array('Content-Type' => 'text/plain; charset=utf-8')
        );
    }

}
```
