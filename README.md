# backup-sample

## レガシー並行外部バックアップのサンプルルーチン

このサンプルは、バージョン2009.1以降で動作するサンプルです。

サンプルルーチンでは、システムルーチン　DBACKを利用してオンラインバックアップ（フル／差分）レガシー並行外部バックアップを実行します。
ルーチン内では、実行時に指定するデータベース名を利用して、バックアップデータベースリストの更新も同時に行っています。


### サンプルルーチンのインポート

	%SYSネームスペースへインポートします。
	（ルーチン名：ZBACKUP　でインポートされます。）


### ルーチン実行

#### ルーチン内のラベル名：help　を実行すると、実行に必要な引数の説明が表示されます。

```USER>zn "%SYS"
 
%SYS>do help^ZBACKUP
set res=$$do^ZBACKUP(flag,dir,dblist)
  flag  : [必須]バックアップ種類 (1=フル/2=差分/3=並行外部バックアップ)
  dir   : [必須]バックアップファイル格納フォルダ (C:\temp\ or /var/tmp/)
  dblist: データベース名をカンマ区切りで指定
      例）　set dblist="USER,TEST"
  res   : 1=成功, -1=失敗
                   : 0=DBACKルーチンの実行失敗（何らかのエラー発生）
  log   : ^ZBACKUPLOG,^ZBACKUPERR参照
 
%SYS>```


#### ルーチン内のラベル名：do を実行すると、バックアップが開始されます。

≪実行例≫　USERデータベースと、DEVデータベースのバックアップ例


```%SYS>set status=$$do^ZBACKUP(3,"c:\temp\backup\","USER,DEV")
 
%SYS>write status
1
%SYS>zwrite ^ZBACKUPLOG
^ZBACKUPLOG=1
^ZBACKUPLOG(1)=7
^ZBACKUPLOG(1,1)="01/13/2012 16:05:25 [START] ZBACKUP が開始されました"
^ZBACKUPLOG(1,2)="01/13/2012 16:05:25 [OK] DBACKルーチンの実行開始"
^ZBACKUPLOG(1,3)="01/13/2012 16:05:26 [OK] CACHE.DATのコピー開始"
^ZBACKUPLOG(1,4)="01/13/2012 16:05:27 [OK] CACHE.DATのコピー終了"
^ZBACKUPLOG(1,5)="01/13/2012 16:05:27 [OK] レガシー並行外部バックアップ開始"
^ZBACKUPLOG(1,6)="01/13/2012 16:05:33 [OK] レガシー並行外部バックアップ終了"
^ZBACKUPLOG(1,7)="01/13/2012 16:05:33 [COMPLETE] ZBACKUPが成功しました"
 
%SYS>```

バックアップ終了後、戻り値を確認します。（1は成功）
バックアップログは、^ZBACKUPLOG　に記録されます。
ログに記載できないような予期しなかったエラーは　^ZBACKUPERR　に記録されます。

