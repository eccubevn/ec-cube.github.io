---
layout: default
title: セキュリティ
---

# {{ page.title }}

イベントを使って拡張を行った場合、画面によりセキュリティ制御を行う必要があります。

(Khi mở rộng mà sử dụng event, cần tiến hành security control trên màn hình)

EC-CUBE3では管理画面及びマイページに関してはログインが必須となっており、利用するイベントによってログイン状態を判断する必要があります。

(Vì trong EC-CUBE bắt buộc login đối với màn hình Quản lý và My page nên cần phải phán đoán tình trạng login dựa trên event sử dụng)

ログイン必須かどうかの指定は`\Eccube\Application.php`内で行っています。

(Chỉ định có bắt buộc login hay không diễn ra trong `\Eccube\Application.php` )

```php
$this['security.access_rules'] = array(
    array("^/{$this['config']['admin_route']}/login", 'IS_AUTHENTICATED_ANONYMOUSLY'),
    array("^/{$this['config']['admin_route']}/", 'ROLE_ADMIN'),
    array('^/mypage/login', 'IS_AUTHENTICATED_ANONYMOUSLY'),
    array('^/mypage/withdraw_complete', 'IS_AUTHENTICATED_ANONYMOUSLY'),
    array('^/mypage/change', 'IS_AUTHENTICATED_FULLY'),
    array('^/mypage', 'ROLE_USER'),
);
```

### セキュリティ対応が必要なイベント (Event cần đối ứng Security)

- eccube.event.render.[route].before
- eccube.event.route.[route].request (After 3.0.9)
- eccube.event.route.[route].response (After 3.0.9)


これらのイベントを利用する場合、画面によってはログインされているかどうかの確認が必要になります。

(Khi sử dụng những event này, cần kiểm tra có đang Login không bằng màn hình)

- 管理画面の場合  (Trường hợp màn hình quản lý)
全ての画面に対してチェックが必要になります。イベント実行時の最初に、

(Cần check ở tất cả các màn hình. Đầu tiên của chạy event là cần check

{% highlight php %}
if (!$this->app->isGranted('ROLE_ADMIN')) {
    return;
}
{% endhighlight %}

とログインしている人をチェックする必要があります。このチェックがないと、ログインしていないにも関わらずイベントの中身が実行されてしまいます。

và người đang login. Nếu không có check này thì sẽ có thể thực hiện bên trong event mà không cần login nữa)

- フロント画面の場合  (Trường hợp màn hình Front)
マイページなどの画面に対してチェックが必要になります。イベント実行時の最初に、

(Cần check đối mới những màn hình như là My page. Đầu tiên của chạy event là cần check

{% highlight php %}
if (!$this->app->isGranted('ROLE_USER')) {
    return;
}
{% endhighlight %}

と管理画面と同様にチェックする必要があります。

và cần check giống với màn hình quản lý)

