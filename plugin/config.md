---
layout: default
title: プラグインの設定、定義
---

# {{ page.title }}

プラグインを動作させるためには最低限必要な設定ファイルが存在し、そのファイルにプラグインの設定を記述します。

Trong các file setting tối thiểu cần thiết để có thể chạy Plugin có miêu tả setting cho Plugin.

### プラグインの設定 (Cài đặt Plugin)
プラグインを作成するには必ず`config.yml`ファイルが必要です。

Để tạo được Plugin nhất định cần có file`config.yml`.

```
[プラグインコード]
  └── config.yml
```

このconfig.ymlにプラグインの設定を記述します。記述する内容としては以下の通りです。  

Trong config.yml này có mô tả setting cho Plugin. Nội dung sẽ như dưới đây.

この内容はすべての設定を記述していますが、必要のない設定は定義する必要はありません。

Tuy nội dung này mô tả tất cả setting nhưng cũng không cần định nghĩa những setting không cần thiết.

```
name: [プラグイン名]
code: [プラグインコード]
version: [バージョン(1.0.0や1.1.5など)]
service:
    - XXXXServiceProvider
event:
    - XXXXEvent
orm.path:
    - /Resource/doctrine
const:
    aaaa: true
    bbbb: 10000
    cccc: 決済可能
```
versionは数値(0.1や1.0.0等々)で定義します。  

version được định nghĩa bằng giá trị số (0.1 hoặc 1.0.0, vv..)

プラグインを動作させる上でconfig.ymlで最低限必要な内容は、

Nội dung cần thiết tối thiểu trong config.yml để chạy plugin như dưới đây:

```
name: [プラグイン名]
code: [プラグインコード]
version: x.x.x
```
となります。


### メッセージの設定 (Cài đặt message)
EC-CUBE3本体ではエラーメッセージは極力ソースコードには記述せず、  
`ECCUBEROOT/src/Eccube/Resource/locale/message.ja.yml` に定義して利用しています。  

Trong hontai EC-CUBE3, error message không được ghi trong source code mà đang được định nghĩa ở `ECCUBEROOT/src/Eccube/Resource/locale/message.ja.yml` để sử dụng.

プラグイン側でも同様にメッセージ定義が必要になった場合、本体側にメッセージを追加できないため以下のディレクトリに配置して定義します。

Trường hợp ở plugin cần định nghĩa message tương tự, vì không thể thêm message vào Hontai nên ta đặt nó vào directory dưới đây để định nghĩa.

```
[プラグインコード]
  ├── Resource
  │   └── locale
  │       └── message.ja.yml
```

プラグインから`message.ja.yml`を読み込むためには、`ServiceProvider`で以下のように定義します。(サービスプロバイダーについては[サービスプロバイダー](serviceprovider)で解説します)

Để đoc được `message.ja.yml` từ plugin, ta định nghĩa như sau tại `ServiceProvider`. (Dùng [サービスプロバイダー](serviceprovider) để giải thích service provider)

```
// メッセージ登録
$file = __DIR__.'/../Resource/locale/message.'.$app['locale'].'.yml';
$app['translator']->addResource('yaml', $file, $app['locale']);
```

### 定数定義 (Định nghĩa định số)
プラグインでは滅多に変更しない値をオプションとして定義する場合、`config.yml`に定義して利用可能です。

Trường hợp định nghĩa giá trị hiếm khi thay đổi trong plugin, ta có thể định nghĩa `config.yml` để sử dụng.

```
const:
    aaaa: true
    bbbb: 10000
    cccc: 決済可能
```

プラグインからは、以下で呼び出すことが可能です。

Từ Plugin, có thể gọi như bên dưới

```
$hoge  = $app['config']['プラグインコード']['const']['aaaa'];
```
