---
layout: default
title: テンプレート
---

# {{ page.title }}

プラグインではテンプレートを差し込む場合、イベントを通じて差し込みます。  

Trong Plugin, khi muốn thêm template, phải làm thông qua event.

### テンプレートファイルの配置場所 (Vị trí đặt file Template)
テンプレートファイルは本体と同じくResourceディレクトリ配下に置きます。

Template file đặt trong diretory Resource giống như ở Hontai.

```
[プラグインコード]
  ├── Resource
  │   └── template
  │           ├── admin
  │           │   └── XXXX.twig
  │           └── XXXX.twig
```


### テンプレートイベント (Template event)

EC-CUBE3.0.9からは既存画面に対して項目などの追加を行う場合、テンプレートイベントを利用します。

拡張を行いたい画面に対してイベントを指定するには、本体側のControllerにある

```php
return $app->render('Shopping/complete.twig', array(
```
とtwigファイルを指定している部分がそのままイベント名となります。  
 
event.ymlには、

```yaml
Shopping/index.twig:
    - [onShoppingIndexRender, NORMAL]
```
とイベント名を指定します。管理画面の場合、

```php
return $app->render('Order/index.twig', array(
```
とController側でtwigが指定されていた場合、  
event.ymlには

```yaml
Admin/Order/index.twig:
    - [onAdminOrderIndexRender, NORMAL]
```
と`Admin/`をつける必要があります。

(Vietnamese trans)
Từ EC-CUBE3.0.9, khi thêm các mục vào màn hình vốn đó, ta dùng Template event.

Để chỉ định event cho những màn hình muốn mở rộng, phần được chỉ định 
```php
return $app->render('Shopping/complete.twig', array(
```
và twig file trong Controller của Hontai sẽ như vậy mà trở thành tên event.

Trong event.yml có chỉ định tên event và
```yaml
Shopping/index.twig:
    - [onShoppingIndexRender, NORMAL]
```
Trường hợp của màn hình quản lý  

```php
return $app->render('Order/index.twig', array(
``` 
và trường hợp twig được chỉ định ở Cotroller  thì ở 
event.yml cần thêm vào

```yaml
Admin/Order/index.twig:
    - [onAdminOrderIndexRender, NORMAL]
```
 và `Admin/`
 --------------------
### テンプレートイベントによる拡張 (Mở rộng bởi template event)
既存画面に対して拡張を行うにはテンプレートイベントに対する処理を実装する必要があります。 formを項目として追加したい場合、

Để mở rộng màn hình vốn có, cần phải thực thi xử lý đối với template event. Trường hợp muốn add form như là item thì bổ sung như sau sẽ có thể add được.

- XXXXEvent.php
{% highlight php %}
public function onShoppingIndexRender(TemplateEvent $event)
{
    $parameters = $event->getParameters();
    $form = $parameters['form'];
    $parts = $app['twig']->getLoader()->getSource('[プログラムコード]/Resource/template/parts.twig');
    // twigコードに挿入
    // 要素箇所の取得
    $search = '<div id="xxxx">';
    $replace = $parts.$search;
    $source = str_replace($search, $replace, $event->getSource());
    $event->setSource($source);
}
{% endhighlight %}

- parts.twig
{% highlight twig %}
<h2 class="heading02">ニックネームの入力</h2>
<div>{% raw %}
    {{ form_widget(form.nickname) }}
    {{ form_errors(form.nickname) }}{% endraw %}
</div>
{% endhighlight %}

XXXXEvent.phpにparts.twigの内容も記述しても良いのですが、twigファイルとEvent部分を分離することで可読性がよくなります。

また、JavaSciptタグを追加したい場合、

- XXXXEvent.php
{% highlight php %}
public function onShoppingIndexRender(TemplateEvent $event)
{
    $source = $event->getSource();

    $tag = <<< EOT
{% raw %}{% block javascript %}
<script>
    $(function() {
        alert("hoge");
    });
</script>
{% endblock javascript %}{% endraw %}
EOT;

    $event->setSource($source . $tag);
}
{% endhighlight %}



と記述すると追加可能です。
