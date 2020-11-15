
## solr起動
    solr start -p 8983

## solr 状態確認
    solr status

    以下のように表示されればOK
    Found Sol process XXXX runing on port xxxx

## curlコマンド
    様々なプロトコルを使用してデータ転送を行うことができるコマンド
    HTTPのGETやPOSTはもちろん、HTTPSやFTP, Telnet, SMTP, IMAP,POP3など様々な通信プロトコルをサポートしています。

    WindowsだとUnicode環境じゃないと正常動作しないかも
    PoserShellを使うのいいかな



## コア関係コマンド

### コア作成
    testAというコアをRESET　APIで作成するコマンド
    ただし、${SOLR_HOME}　直下にコア名のフォルダと設定ファイルがないと機能しない

    curl 'http://localhost:8983/solr/admin/cores?wt=json&action=CREATE&name=test_A'

### インデックス削除
    defaultというコアのインデックスを削除する場合
    http://localhost:8983/solr/default/update?stream.body=<delete><query>*:*</query></delete>&commit=true