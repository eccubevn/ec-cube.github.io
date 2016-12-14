---
layout: default
title: リソースファイル、ブロック
---

# {{ page.title }}

プラグインで画像ファイルやCSSファイルなどのリソースファイルを利用する場合、EC-CUBE本体の公開ディレクトリへリソースファイルをコピーする必要があります。

Trường hợp sử dụng resource file trong plugin như file screen hay file CSS, cần phải copy resource file đến public directory của Hontai EC-CUBE

### リソースファイルの配置場所 (Vị trí đặt resource file)

```
[プラグインコード]
  ├── Resource
  │   ├── assets
  │   │   ├── css
  │   │   │   └── xxxx.css
  │   │   ├── img
  │   │   │   ├── xxxx.gif
  │   │   │   ├── xxxx.jpg
  │   │   │   └── xxxx.png
  │   │   └── js
  │   │       └── xxxx.js
```

`assets`ディレクトリ配下のものを本体側の公開ディレクトリへコピーします。

Copy cái trong directory `assets` đến public directory của Hontai 

### 本体側へのコピー (Copy đến phía Hontai)

本体側へコピーする方法はいくつかありますが、[プラグインマネージャー](pluginmanager)内で行うのが一番簡単です。

Có nhiều cách để copy đến Hontai nhưng đơn giản nhất là tiến hành trong [プラグインマネージャー](pluginmanager)

コピー先のディレクトリですが、

Directory của nơi copy

```
ECCUBEROOT
├── app
│   ├── Plugin
│   │   ├── [プラグインコード]
│   │   │   ├── Resource
│   │   │   │   ├── assets
│   │   │   │   │   ├── css
│   │   │   │   │   │   └── xxxx.css
│   │   │   │   │   ├── img
│   │   │   │   │   │   ├── xxxx.gif
│   │   │   │   │   │   ├── xxxx.jpg
│   │   │   │   │   │   └── xxxx.png
│   │   │   │   │   └── js
│   │   │   │   │       └── xxxx.js

〜
〜

├── html
│   ├── plugin
│   │   ├── [プラグインコード(全て小文字)]
│   │   │   └── assets
│   │   │   ├── css
│   │   │   │   └── xxxx.css
│   │   │   ├── img
│   │   │   │   ├── xxxx.gif
│   │   │   │   ├── xxxx.jpg
│   │   │   │   └── xxxx.png
│   │   │   └── js
│   │   │       └── xxxx.js
```


`html`ディレクトリ直下の`plugin`ディレクトリ内にプラグインコードでディレクトリを作成して配置します。ディレクトリを作成する場合、全て小文字でプラグインコードを作成した方が大文字を区別しなくて済むのでオススメです。

Trong directory `plugin` ngay dưới directory `html`, tạo directory bằng plugin code rồi đặt vào. Trường hợp tạo directory, tôi khuyên nên viết plugin code tất cả bằng chữ nhỏ để không cần phân biệt với chữ lớn nữa.


### ブロックの場合 (Trường hợp của Block)

リソースファイルではないのですが、プラグインでブロックを追加したい時があります。その場合、リソースファイルと同様に本体側へコピーする必要があります。
こちらも同様にプラグインマネージャー内でコピーを行う方法が簡単です。

Không phải resource file nhưng trong plugin cũng có lúc muốn thêm block vào bằng plugin. Khi đó, cũng cần phải copy đến Hontai giống như resource file. Tương tư, cách tiến hành trong Plugin managemer cũng đơn giản nhất.

```
ECCUBEROOT
├── app
│   ├── Plugin
│   │   ├── [プラグインコード]
│   │   │   ├─ Resource
│   │   │   │        └── template
│   │   │   │           ├── Block
│   │   │   │           │   └── XXXX.twig
│   │   │   │           ├── admin
│   │   │   │           │   └── XXXX.twig
│   │   │   │           └── XXXX.twig
│   ├── template
│   │   ├── default
│   │   │   ├─ Block
│   │   │   │      └── template
│   │   │   │         ├── admin
│   │   │   │         └── default
│   │   │   │             ├── Block
│   │   │   │             │         └── XXXX.twig
```

- ブロックファイルのコピー例

```
$file = new Filesystem();
// ブロックファイルをコピー
$file->copy('コピー元ファイル', $app['config']['block_realdir'] . '/' . 'コピー先ファイル名');
```


また、ブロック追加時は`dtb_block`テーブルへ追加する必要があります。



```php
$DeviceType = $app['eccube.repository.master.device_type']->find(DeviceType::DEVICE_TYPE_PC);

$Block = $app['eccube.repository.block']->findOrCreate(null, $DeviceType);

// Blockの登録
$Block->setName('ブロック名')
                ->setFileName('ブロックファイル名');
                ->setDeletableFlg(Constant::ENABLED);

$em->persist($Block);
$em->flush($Block);
```

※本体側でトランザクション制御をしれくれるため、ロールバック処理などは必要ありません。


### リソースファイルの削除

プラグインを削除する場合、リソースファイルを削除するのを忘れないでください。

