機能概要
・テーブルを指定することでそのテーブル定義を取得し、その定義に応じたテストデータ（xls)を作成する。

使い方
・Mainクラスを実行する

パラメータ
　fileName
　　作成するXLSファイル名
　targetTables
　　テストデータ作成の対象テーブル（複数可能）
　rowsize
　　各シートの作成データ行数

設定ファイル
 main/resources/
　・domainValue.conf
　　ドメインの値リスト生成に利用。ドメインの値リストを使わない場合は、メンテナンス不要。
　　ドメイン論理名とそれに紐づく値リストを管理。
　　テストデータ作成時に、ここに記載したドメインに紐づく値リストが存在すれば、そのリストから値を取得してテストデータを生成する。

　　例：
　　　処理済みフラグ=["0","1"]　ならば、0または1のいずれかをテストデータへ設定する。
　　　処理済みフラグ=["",""]　ならば、ランダムに型に応じた値を生成して設定する。

　・tableToDomain.properties
　　ドメインの値リスト生成に利用。ドメインの値リストを使わない場合は、メンテナンス不要。
　　テーブル物理名＋カラム物理名をドメイン論理名へマッピングしたもの。
　　カラム定義の追加・更新があり、それを利用してドメインの値リストを使いたい場合は、メンテナンス必要。

　・jdbc.properties
 　　DB接続定義

データ作成の仕様
各型ごとに生成仕様を定義している。詳しくは、service.型名.impl配下を参照
・DECIMAL型
　数値を列単位で昇順設定。
　小数点を持つものは、小数点以下のみ有効桁数でランダムに設定。
　例：
　　整数：
　　　1,2,3,4,5...
　　小数：
　　　1.234,2.331,3.554,4.958...　左記のように、整数は連続値、小数点以下は有効桁数でランダム。

・VARCHAR,NVARCHAR型
 列物理名の1文字目を小文字にして、0埋めした連番。
 例：
 system_code：
  s1,s2,s3...
 created_by：
  m0000001,m0000002,m0000003
 user_je_category_name:
  u00000000000000000001,u00000000000000000002　・・・　0埋めする長さは見栄えを優先し、型長と列物理名の文字長の短い方。
  
・CHAR型
　VARCHAR,NVARCHAR型と同じだが、生成するデータ長は必ず列定義のデータ長と同じ長さで生成する。

・DATETIME型
　2018/05/10を基準日として、列方向へ1日ずつ加算して利用する。
　列が変われば、変更前の列に＋1日したものを設定する。
 RandomDateCreatorに基準日は記載。

・DECIMAL、DATETIME、VARCHAR、NVARCHAR、CHAR以外の型は未対応。
　追加する場合は、service.データ型.implパッケージを作成して、service.string.impl配下を見本にクラスを追加してください。
　また、Main.javaのmain.Main.preCreateTestData(TableDefinitionDto, int)メソッドへ呼び出しを追加してください。
