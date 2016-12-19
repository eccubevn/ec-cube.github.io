---
layout: default
title: EC-CUBE3のバージョン違いによる動作
---

# {{ page.title }}

EC-CUBE3.0.8までとEC-CUBE3.0.9以上からプラグイン機構(プラグインの実装)が変わっており、古いバージョンもサポートされていますがサポートするEC-CUBEのバージョンにより対応するイベントが異なります。また本体側でサポートされた機能も存在します。

(Cấu tạo của EC-CUBE từ ban đầu cho tới 3.0.8 và từ 3.0.9 trở đi có sự khác nhau. Tuy version cũng có được hỗ trợ nhưng tùy vào version mà event sẽ khác nhau. Ngoài ra, cũng có chức năng được hỗ trợ trong Hontai)

イベントについては[イベント](event)で詳しく解説しますが、EC-CUBEのバージョンによって動作方法で以下の違いがあります。

(Tại [イベント](event) có giải thích cụ thể về event nhưng tùy vào version EC-CUBE mà thao tác sẽ khác nhau như dưới đây)

#### EC-CUBE3.0.8まで (EC-CUBE cho đến 3.0.8)
リクエストやレスポンスに対してのみイベントがあり、プラグインからはそのイベントに対してのみ処理を差し込み可能です。

(Chỉ có event dành cho request và response, từ Plugin chỉ có thể thêm xử lý vào event này)


#### EC-CUBE3.0.9から (EC-CUBE từ 3.0.9 trở đi)
新たなイベントを新規追加、フックポイントの新規追加、コントローラーやtwigファイル単位でのイベントを追加等々機能が大幅に改善され、  
プラグインから本体に対して処理を介在する箇所が増えています。

(Được cải thiện mở rộng nhiều chức năng như thêm event mới, thêm hookpoint mới, thêm event đơn vị controller và twif file; ngoài ra chổ tồn tại trung gian giữa plugin và hontai cũng tăng lên)


#### EC-CUBE3.0.11から (EC-CUBE từ 3.0.11 trở đi)
トランザクション管理が本体側でサポートされ、それに伴いプラグイン側も考慮する必要があります。  

(Vì trong hontai bắt đầu có hỗ trợ về quản lý transaction nên plugin cũng cần đối ứng)

詳しくは、 [EC-CUBE 3.0.11 変更内容に伴うプラグインへの影響](/guideline/plugin-update-for3.0.11){:target="_blank"} を参照してください。

(Chi tiết tham khảo [EC-CUBE 3.0.11 変更内容に伴うプラグインへの影響](/guideline/plugin-update-for3.0.11){:target="_blank"})

#### EC-CUBE3.0.12から (EC-CUBE từ 3.0.12 trở đi)
本体側にログ機能が用意され、プラグインからログを簡単に出力する事ができるようになりました。

(Hontai có chức năng log, sau đó ở plugin cũng trở nên có thể dễ dàng output log)

詳しくは、 [EC-CUBE3でのログ設定](/log){:target="_blank"} を参照してください。

Chi tiết tham khảo [EC-CUBE3でのログ設定](/log){:target="_blank"} 

### プラグインをどの本体のどのバージョンまでサポートするか (Plugin support tới version nào và của Hontai nào?)
この記事を執筆時点でEC-CUBE3.0.9がリリースされてから半年以上経過しており今後新しくプラグインを作成する場合、EC-CUBE3.0.9以上のみを対象に先ずは作成しましょう。  

(Vào thời điểm tạo document này, 3.0.9 chỉ vừa release hơn nửa năm. Từ nay, khi tạo mới plugin, chỉ tạo dành cho đối tượng từ 3.0.9 trở lên)

その後、3.0.8以下でもプラグインを対応して欲しいという要望が出た時点で対応すればいいでしょう。

(Nếu sau đó có yêu cầu đối ứng từ version trừ 3.0.8 trở về trước thì mới đối ứng)
