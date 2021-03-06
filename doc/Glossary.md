
# Apache Solrとは
   検索サーバで、以下の特徴を備える
  1. Apache Lucene（アパッチ　ルシーン）を用いた完成された検索エンジンサーバ
  2. 膨大な検索結果の中からユーザーが求めているっぽいドキュメントを適切に誘導する様々な仕組みを備える
  3. I/Oレスで高速な検索を行う様々なキャッシュを備える
  4. インデックスのレプリケーション構造を備える
  5. 分散検索により大量のドキュメントを扱える
  6. Apache License 2.0のOSS＆機能拡張のためのインター―フェース・活発？なコミュニティ

-----
# Solr単語集
## a-z

## analyzer
    転置インデックス作成前の前処理
    フィールドタイプ（データの形式）ごとに指定することができる。

    具体的には以下3種類
    - charFilter 　
      - 文字列の正規化をするところ
      - たとえば全角・半角統一
      - 日本語の場合、必須。
    - tokenizer
      - 単語分割のプログラムのこと
      - 形態素解析/N-gram など
      - 必ず1つ指定する
      - 形態素解析では半角単語がないことが多い
    - filter（トークンフィルタ）:
      - ストップワード除去
      - 助詞みたいなワードは使わないので
      - 落としたい単語を削る

## autoGeneratePhraseQueries
　トークナイズ後の検索条件を AND にするか OR にするかのパラメータ
　デフォルトはfalse(OR)になっている


## DIH
    データインポートハンドラのこと

## edismax
    Extended Disjunction Max Queryのこと
    フィールド横断検索のとき使う
    dismaxより有用？

## Lucene
    Javaで描かれた検索エンジンライブラリ

## managed-schema
    Solr 6系以降のスキーマ定義ファイル
    インデックスの構造を定義する

## SpellCheck
    もしかして検索を再現できる機能
    他の類似する単語をクエリを元にサジェストすることを目指している
    もしかして単語を表示するかどうかはアプリ側で実装する

## あ

## インデクシング機能
    検索対象文書から転置インデックスを作成する機能

## オートコンプリート
    クエリキーワードを入力中に候補を表示する機能（入力保管、サジェスチョン）を提供します。  
    日本語でもある程度対応可能です。
    SuggestComponentという機能を利用します。

## か

## キャッシュ
    以下などをキャッシュとして持つ
    - queryResultCache : 検索条件(Query+Filters+Sorts)
    - filterCache : フィルタ検索条件(Filters)
    - documentCache : docID
      - 非autowarm
    - filedValueCache：ファセット用キャッシュ
    - user cache : ユーザー定義キャッシュ
      - 非autowarm
    autowarmという自動ウォームアップが可能なものと、そうではないものがある
    ユーザー定義キャッシュはユーザーの提供次第

    日付型フィールドにNOWを使ったクエリを発行すると、キャッシュが利用されない場合があります。
    NOWがクエリ発行時の時刻に変換されるためです。
    例：   
        fp = last_modified:[* TO NOW]
        ↓
        fp = last_modified:[* TO 132670121123]　に変換される
        30秒後に再度発行
        fp = last_modified:[* TO NOW]
        ↓
        fp = last_modified:[* TO 132670151123]　に変換される
        ！終値が変わってしまう！！！

    そのため、
    　　fp = last_modified[* TO NOW/DAY ]
    など日時を丸め込んで利用する必要がある。

    インデックスが更新されるとキャッシュが無効になる。

## 空間検索/地理検索
     Googleマップ的な何か
     
## クエリパーサ
    ユーザが入力した検索キーワードの文字列を、Solrが解析できる構造に変換する
    検索時のURLにdefTypeで指定することでdismaxなどのクエリパーサを利用可能

    例： q=プログラム Ruby&qf~=title text&defType=dismax

## グループ検索
    指定されたフィールドの値が同じものをグループ化して検索結果を折りたたむ

## コア
    コアとは、検索エンジンそのものを差します。
    サーチャーやインデクサーの定義、インデックスデータの管理、プラグインの管理などを行う単位です。

## コマンド
    起動：${SOLR}/bin/solr start -s ${SOLR_HOME}
    再起動： ${SOLR}/bin/solr restart  

## コミット
    コミットは3種類
    - HardCommit：ディスクにコミット
    - SotfCommit : メモリ上のコミット
    - autoCommit

## さ

## シノニム
    シノニム辞書とは類義語をまとめた辞書
    たとえば「首相」で検索しても「内閣総理大臣」もヒットさせたい
    ときに使う機能
    たとえば「スマートフォン」と検索したときに「iPhone」もヒットさせたいときもがあるが、
    「iPhone」と検索したときは「スマートフォン」は検索したいくない、みたいなアンチパターンをシノニムに重みを設定することで対応することもできる
    

## 順次検索（じゅんじけんさく）
    文書検索方式の一種
    検索対象の先頭から後方に向かって探索していき、一致しているかどうか調べる方式
    grepコマンドやRDBのLIKE検索など

## スキーマ
    データベースの構造のこと
    solrにおいてはインデックス構造のことを指す
    スキーマの定義がなければデータの投入も検索もできない

## スキーマ定義ファイル
    managed-schema (Solr 6系以降)    
    更新したあとはSolrの再起動か、コアのリロードが必要
    SchemaAPIを使用している場合はすぐにSolrに反映されるので不要

## スコアブースティング
    キーワードに対する関連度の調整
    検索語やフレーズに重みづけをして、検索ヒット順を調整する

## た

## ディレクトリ
    - ${SOLR} : solrインストールディレクトリ
    - ${SOLR_HOME} : solrのhomeディレクトリ
      - bin/solr start コマンド時の-sでディレクトリを指定
      - 指定がない場合、${SOLR}/server/solr が指定される
    - ${SOLR_DATA} : インデックスができる親のディレクトリ
    - ${SOLR_DATA}/index : インデクスディレクトリ

## 単語分割機能
    文字列を単語単位に分割する機能
    転置インデックスを作成するためには、検索対象の文書や文字列を「単語」に分割する必要がある
    日本語では以下2種類が主流
    - 形態素解析
    - 文字N-gram
    Solraではたぶん形態素解析を使った方がいいかも
  
## データインポートハンドラ(DIH)
    データソース(RDB)からデータを読み込んでsolrにロードする機能全件読み込むfull-importと、前回のロード時との差分を読み込むdetla-importがある

## 転置インデックス（てんちいんでっくす）
    Solrで採用されている文書検索方式
    あらかじめ転置インデックスという単語の索引を作成しておき、検索時はそのインデックスを使って検索するシステム

    インデックスに登録するドキュメントは以下ファイルで登録することができる、削除や更新も以下ファイルで可能
    ただし、削除・更新を行う場合はフィールドにユニークキーが設定されている必要がある

    - jsonファイル
      - BOMなしのUTF8で記述
      - postすることで登録できる
    - XML
    - csv
      - ただし、boostによる重要度を指定できない
      - BOMなしUTF8で記載


## トークン
    フィールドの文書中の単語の出現
    Lucene/Solrでは以下標準的な属性を持つ
    - 単語のテキスト
    - 開始オフセット（トークン開始文字位置）
    - 終了オフセット
    - 位置増分
    位置はフレーズ検索等で使用される

## ドキュメント
    検索対象のこと。
    RDBのテーブルデータにおけるレコードに相当する

## トランザクション管理
    厳密なトランザクション管理機構はない
    __version__フィールドを利用する場合は、楽観的ロック制御が可能

## は

## ハイライト
    検索結果の一覧を表示するとき、キーワードが目立つように大文字や色を変えたりするなど強調する機能

## 標準検索式
    以下を組み合わせて使える

    -　単語の検索
    - フレーズの検索
    - 括弧の利用（AND、OR）
    - フィールド指定
    - 範囲指定
    - 重み指定 (例： プログラミング OR Java^5 )
    - フレーズ近接 (例: "自然言語"~3)
    - ワイルドカード検索 (例：アー*   al?)
    - 「すべて」検索　( *:*  )

## 日付形式
    UTC時刻に変換して登録する(Solr推奨)
    そうじゃなくても使えるけど、たとえばJSTで統一などが必要
    海外で使う場合は、時刻の基準がUTCじゃないと旨く検索できないだから基準を揃えるという意味でUTCで保存

    現在のUTC日時を表すのに「NOW」という文字列を利用することができる    
    例：
        fq=last_modified:NOW 
        クエリパーサーによって以下のようなクエリに変換される
        fq=last_modified:"2020-11-12T15:18:20.729Z"

## ファセット
    属性での絞り込み検索のリンクを提示する機能
    Amazonなどで商品を検索した時に出るメーカーや価格帯のリンクなどがそれ

## フィールド
    - ダイナミックフィールド
      - フィールド名を動的に設定できるフィールド
        - フィールド名に「*」を1つだけ用い、インデックス時 or 検索時は前方一致又は後方一致で動的に探索されます。
      - ユニークキーフィールド
        - データの削除・更新用
        - このフィールドはオプションだが、業務用システムではほぼ必須
      - コピーフィールド
        - 定義済みフィールドにポストされたデータを自動で他の定義済みフィールドにポストしたい場合に使う
        - たとえば形態素解析　と　n-gramを併用して使用したい場合などで使う
        - 標準は形態素解析で検索し、コピーした方でn-gramで検索する・・・など

## フィールドタイプ
    インデックスデータを保存する形式のこと
    数値・文字列・日付など
    細かく色々定義できる
    
    大きく分けて以下二種類存在する。

    - 非テキスト型フィールドタイプ
      - 数値や単語分裂が必要ない文字列を用いるフィールドタイプのこと
      - name / classの指定が必須
    - テキスト型フィールドタイプ
      - 文字列のフィルタリングや単語分裂を行うフィールドタイプ
      - analyzerではどのように文字列をどのようにフィルタリング・単語分割するかを定義する

## フレーズ検索
　フレーズ検索というのは，次のようにダブルクオーテーションで2語以上からなる検索質問語を
　くくって，指定された語が連続して現れるドキュメントを検索するように指示できる機能のことだ：

　"Java プログラミング"

　ただし、Luceneのフレーズ検索では，フレーズを構成する語は互いに離れていてもよい。
　1つのフレーズとみなせる語と語の最大距離をslopと呼び，
　PhraseQueryのsetSlop()メソッドでフレーズ検索時に許容するslopを設定する。
　デフォルトのslopは0であり，その場合は，PhraseQueryにadd()メソッドで追加した語が追加した順番に隙間なは現れている Documentが検索される。
　
## ページナビゲーション
    ユーザを「次ページ」や「前ページ」の検索結果一覧にナビゲーションするリンクのこと
    ページの異動は検索を再実行することで行う
    したがって。リンクには同じ検索を実行するための情報を埋め込んでおく

## ま

## もしかして検索
    SpellCheckコンポーネントで実現可能
    詳しくは[SpellCheck](#spellcheck) で

## ら

## ランダムソート
    検索結果一覧の順序をリクエストのたびに並び順を変える機能
    たとえば、スポンサー企業の商品などを公平に表示するために必要
    セッション（クライアント）ごとの検索リクエストでsortパラメータにrand{乱数}を指定することで、セッションごとに違う並びにしつつ、同じセッションでは追加で検索しても同じ並びになるようにします。

## リランキング
    クエリAでヒットしたドキュメントの中から上位N件のドキュメントを再度クエリBで検索してランキングを再計算できる
    メリットとしては、クエリAが軽くクエリBが想い場合、クエリBだけを実行するよりも高速に動作します。

## レプリケーション
    同じ内容のインデックスのコピーを別に持つこと