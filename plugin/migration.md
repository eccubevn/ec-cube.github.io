---
layout: default
title: マイグレーション
---

# {{ page.title }}

プラグイン用のテーブル作成・削除を行う場合、マイグレーションという仕組みを使います。マイグレーションには極力テーブル追加、削除処理に止めておきます。

Khi tiến hành xóa/tạo table dùng trong Plugin, ta sử dụng cách thức gọi là Migration. Add table vào migration, dừng trước xử lý xóa.

但し、プラグインがバージョンアップされた時にしか実行されない処理があれば、マイグレーションで行う方が簡単です。

Tuy nhiên, nếu chỉ là xử lý diễn ra khi version up Plugin thì cách chạy migration rất đơn giản.

- マイグレーションではテーブル作成、削除を行う
- (Tiến hành tạo, xóa table trong migration)
- プラグインバージョンアップ時にPluginManagerのupdate関数で対処できないケースのみマイグレーションファイル内に記述  
- (Chỉ ghi vào trong migration file những case không thể xử lý bằng hàm số update của PluginManager khi version up Plugin)
→複数回バージョンアップが発生した時にupdate関数では対処し辛い等

(Khó mà xử lý bằng hàm update khi phát sinh nhiều version up)

### マイグレーションファイルの配置場所 (Nơi đặt file Migration)
マイグレーションファイルは本体と同じくResourceディレクトリ配下に置きます。

Migration file cũng giống với Hontai là đặt dưới Resource directory.

```
[プラグインコード]
  ├── Resource
  │   ├── doctrine
  │   │   └── Plugin.XXXX.Entity.XXXX.dcm.yml
  │   │   └── migration
  │   │       └── VersionYYYYMMDDHHMMSS.php
```
※本体ではマイグレーションファイルは
Trong Hontai, chỉ cần Migration chạy command 
```
php app/console migrations:generate
```
というコマンドを実行すれば自動的に作成されますが、プラグインの所定のディレクトリに対して作成する機能は提供されていません。  

thì nó sẽ tự động được tạo nhưng đối với directory cố định của Plugin thì không cung cấp chức năng tạo.

上記コマンドを実行し、本体側に出来たマイグレーションファイルをプラグインの所定のディレクトリまでコピーするか、既存ファイルをコピーをしてクラス名等を置き換えて作成してください。

Một là chạy command trên, copy migration file đã được tạo trên Hontai sang directory cố định của Plugin; hai là copy file hiện tại, đổi tên class rồi tạo.

### マイグレーションでのテーブル作成・削除 (Tạo/xóa table trong Migration)

プラグイン用テーブルの作成・削除の処理内容は[プラグイン仕様書](http://downloads.ec-cube.net/src/manual/v3/plugin.pdf){:target="_blank"}の「3.プラグインを作る」の「マイグレーション」を参照してください。

Về nội dung của xử lý tạo/xóa table trong Plugin, hãy tham khảo「マイグレーション」(Migration) của 「3.プラグインを作る」 trong [プラグイン仕様書](http://downloads.ec-cube.net/src/manual/v3/plugin.pdf){:target="_blank"}

### 項目の追加・削除 (Thêm/xóa mục)

テーブルの追加・削除以外に項目追加、削除や制約変更も可能です。

Ngoài thêm/xóa table ta còn có thể thêm/xóa mục và update restraint.

```php
<?php

namespace DoctrineMigrations;

use Doctrine\DBAL\Migrations\AbstractMigration;
use Doctrine\DBAL\Schema\Schema;

/**
 * Auto-generated Migration: Please modify to your needs!
 */
class VersionYYYYMMDDHHMMSS extends AbstractMigration
{

    const NAME = 'テーブル名';

    /**
     * @param Schema $schema
     */
    public function up(Schema $schema)
    {
        // this up() migration is auto-generated, please modify it to your needs
        if ($schema->hasTable(self::NAME)) {
            return true;
        }
        $table = $schema->getTable(self::NAME);
        $table->addColumn('nickname', 'string', array('notnull' => true, 'length' => 50));
        $table->changeColumn('email', 'string', array('notnull' => false, 'length' => 250));
    }

    /**
     * @param Schema $schema
     */
    public function down(Schema $schema)
    {
        // this down() migration is auto-generated, please modify it to your needs
        if (!$schema->hasTable(self::NAME)) {
            return true;
        }
        $table = $schema->getTable(self::NAME);
        $table->dropColumn('nickname');
    }
}
```

