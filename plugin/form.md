---
layout: default
title: フォーム
---

# {{ page.title }}

プラグインではフォームとしての扱い方は2種類存在しており、
- 既存のフォームに対しての拡張
- 新規フォームの作成

となります。新規フォームの作成については[フォームを表示してみよう](/tutorial-4)を参照してください。

Trong plugin, hành động đối với form có 2 loại
- Mở rộng đối với form hiện tại
- Tạo form mới
Đối với việc tạo form mới, tham khảo [フォームを表示してみよう](/tutorial-4)

### 既存のフォームに対しての拡張 (Mở rộng đối với form hiện tại)

EC-CUBE3.0.8までは`FormExtension`というイベントを使ってFormに対して拡張を行えましたが、  
3.0.9からは新たなイベントが用意されたため、そのイベントでフォームの拡張を行います。

Cho đến EC-CUBE 3.0.8, ta luôn dùng event `FormExtension` để mở rộng form nhưng từ 3.0.9 trở đi đã có event mới 
nên ta tiến hành mở rộng form trên event đó.

```php
/* admin.product.edit.initialize */
public function onAdminProductEditInitialize(EventArgs $event)
{
    $builder = $event->getArgument('builder');
    $builder->add('nickname', 'text', array(
        'label' => 'テスト項目',
        'mapped' => false,
    ));
}
```

※`$builder->add('nickname' 'text', array(`の`nickname`部分に`plg_`とつけて定義するとテンプレートを拡張することなく画面にフォーム項目が表示されるようになります。  

Khi định nghĩa mà thêm `plg_` vào `nickname` của `$builder->add('nickname' 'text', array(` thì không cần mở rộng template mà các mục của form vẫn sẽ được hiển thị lên màn hình.

詳しくは[フォームの追加と拡張フォームの追加と拡張](/guideline/plugin-form)を参照してください。

Chi tiết hãy tham khảo [フォームの追加と拡張フォームの追加と拡張](/guideline/plugin-form)

### フォームの入力チェックについて (Về Input check của Form)
イベントを使ってフォームの拡張を行うと、POST時のフォームチェックはEC-CUBE本体側にある  

Khi sử dụng event để mở rộng form thì bên phía Hontai EC-CUBE có form check lúc POST.

```
if ($form->isSubmitted() && $form->isValid()) {
```

という処理で行われるようになります。そのため、プラグイン側での検証は不要になります。

Xử lý như trên sẽ diễn ra. Vì vậy, không cần kiểm chứng bên phía Plugin.

### 拡張したフォームのDB保存処理 (Xử lý lưu DB của Form được mở rộng)
フォーム要素を追加したのであれば、POST時にDBに保存などの処理を行うことがよくあります。  

Khi thêm yếu tố form vào thì thường xảy ra các xử lý như lưu vào DB lúc POST.

その場合、`complete`イベントにて処理を行いフォームで入力された値を処理する必要があります。

Vào trường hợp đó, cần xử lý giá trị được input bằng form đã xử lý ở event `complete` 

```php
/* admin.product.edit.complete */
public function onAdminProductEditComplete(EventArgs  $event)
{
    $form = $event->getArgument('form');

    $nickname = $form->get('nickname')->getData();

    $Profile = $this->app['xxxx.profile']->find(zzz);

    $Profile->setNickname($nickname);

    $this->app['orm.em']->persist($Profile);
    $this->app['orm.em']->flush($Profile);
}
```

プラグイン側でDBの登録/更新処理を行うことでフォームに拡張した項目が登録できます。

Ở phía plugin, có thể đăng ký các mục mở rộng cho form bằng cách thực hiện xử lý đăng ký/update DB
