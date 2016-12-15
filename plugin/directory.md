---
layout: default
title: ディレクトリ
---

# {{ page.title }}

プラグインのディレクトリ構成ですが、極力EC-CUBE3本体のディレクトリ構成に合わせる事を推奨します。但し、全てのディレクトリが必要ではなく必要に応じてディレクトリをプラグイン側に作成してください。

Đối với cấu trúc Directory, tôi khuyên nên làm giống với cấu trúc directory của hontai EC-CUBE3. Tuy nhiên không phải tất cả mà chỉ tạo những directory cần thiết trong Plugin.

### 標準的なディレクトリ構成について (Về cấu trúc Directory tiêu chuẩn)

標準でプラグインを以下のようなディレクトリ構成になります。

Cấu trúc Directory tiêu chuẩn dành cho Plugin như dưới đây:

- ディレクトリ例 (Ví dụ)

```
[プラグインコード]
  ├── Controller
  │   └── XXXXController.php
  ├── Entity
  │   └── XXXX.php
  ├── Form
  │   ├── Extension
  │   │   └── XXXXTypeExtension.php
  │   └── Type
  │           └── XXXXType.php
  ├── Repository
  │   └── XXXXRepository.php
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
  │   ├── doctrine
  │   │   └── Plugin.XXXX.Entity.XXXX.dcm.yml
  │   │   └── migration
  │   │       └── VersionYYYYMMDDHHMMSS.php
  │   ├── locale
  │   │   └── message.ja.yml
  │   └── template
  │           ├── Block
  │           │   └── XXXX.twig
  │           ├── admin
  │           │   └── XXXX.twig
  │           └── XXXX.twig
  ├── Service
  │   └── XXXXService.php
  ├── ServiceProvider
  │   └── XXXXServiceProvider.php
  ├── PluginManager.php
  ├── LICENSE.txt
  ├── XXXXEvent.php
  ├── config.yml
  └── event.yml
```

共通関数をまとめたクラス等が必要になった場合、

Trường hợp cần những class có tổng hợp các hàm số dùng chung, hãy tạo 
```
[プラグインコード]
  ├── Event
  │   └── XXXXEvent.php
  ├── Common
  │   └── XXXX.php
  ├── Util
  │   └── XXXX.php
```
と本体側と極力合わせるようにディレクトリを作成してください。本体側に存在しないようなものについては自由に作成しても問題ありませんが、極力似たようなディレクトリ構成を心がけてください。

như trên và directory sao cho thật giống Hontai. Có thể tạo tự do những cái vốn không tồn tại trong Hontai nhưng ít nhất phải cố gắng tạo cấu trúc thật giống Hontai.

本体側のディレクトリ構成は [ディレクトリ・ファイル構成](/spec-directory-structure) を参照してください。

Về cấu trúc Directory của hontai, hãy tham khảo [ディレクトリ・ファイル構成](/spec-directory-structure).
