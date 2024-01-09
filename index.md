## PHP版監視アプリ

### 作成のきっかけ
IT初心者に対して、システム監視業務が「どのようなもの」で、「どようなことをするのか」を学習させるために、監視アプリを作成してきましたので、公開することにしました。

### アプリの中身について
アプリを動作させることが目的であり、プロプラムは思いつくままコーディングして来たことから統一性がなく、綺麗とは言い難いため、出来上がりの動作を楽しんでいただければ幸いです。
また、基本的なテストだけのため、バグが存在することがあります。PHPとHTMLがベースになっていますので、ご自身で修正、改良して下さい。

### アプリの気になるところ
*  主なテストが自宅内LAN上のPCで実施しており、インターネット上のサーバーからのレスポンス遅延が大きく、特にポート、プロセスの遅延が目立ち、Ping監視以外は使い物にならない場合もあります。
*  XAMPPをベースにいろいろなパッケージで構成しているため、バージョンの違いでエラーになることがありますので、とりあえず指定のバージョンで試して下さい。

### アプリの特徴
*  監視対象がビジュアルに視えます
*  監視対象の配置をカスタマイズ出来ます
*  管理者は１つのログイン、一般操作者は複数のログインが出来ます
*  他サイト監視アプリの監視行為が出来ます
*  イベントログの出漁、イベント発生メール通知が出来ます
*  CPU、Memory、Disk使用率のグラフ表示が出来ます
*  PHP、HTMLベースのため容易に追加、修正が出来ます

### カスタマイズ例
![監視レイアウト例](http://192.168.1.155/kanshiphp/koukaiimg/visualmonitor.png/150)

### 画面レイアウト
*  管理者用

![管理者用画面レイアウト](http://192.168.1.155/kanshiphp/koukaiimg/adminlayout.png/150)
*  一般操作者用レイアウト
  
![一般操作者用画面レイアウト](http://192.168.1.155/kanshiphp/koukaiimg/operatorlayout.png/150)

### アプリ構成

![アプリ構成](http://192.168.1.155/kanshiphp/koukaiimg/kanshimap.png/150)

### アプリ起動フロー

ログインから監視アプリ全体を起動するフローを示します

![管理者用画面レイアウト](http://192.168.1.155/kanshiphp/koukaiimg/runsequence.png/150)

### 自動起動の工夫

*  PHP/HTMLコーディング 5秒間でリフレッシュ　サンプル

```php
echo '<html><head>';
echo '<meta http-equiv="Refresh" content= "5">';
echo '</head><body>';
$sec=date("s秒");
echo "<h1>{$sec}</h1>";
echo '</body></html>';
```


### セッションキーの利用

各画面遷移にユーザーIDを受け渡すため、セッションキーを使用

*  ユーザーID登録　サンプル
```php
function setsess($uid){
  echo '<script type="text/javascript">';
  echo 'sessionStorage.setItem("user","'.$uid.'");';
  echo '</script>';
}
...
$user='hoge';  // user:hogeを登録
setsess($user);
```
*  登録済のセッションキーuserで値を取得し自プログラムへ戻す、 
ユーザ得られなければ、unknowを戻す
　サンプル
```php
function paramGet($_pgm){
  echo '<html>';
  echo '<body onLoad="document.F.submit();>';
  echo "<form name='F' action={$_pgm} method='get'>";
  echo '<input type="hidden" name="param" value="">';
  echo '<input type="submit" name="next" style="display:none;" />';
  echo '</form></body></html>';echo '<script type="text/javascript">';
  echo 'var keyvalue = sessionStorage.getItem("user");';
  echo 'if (!keyvalue) {';
  echo '  keyvalue = "unknown";';
  echo '}';
  echo 'document.forms["F"].elements["param"].value = keyvalue;';
  echo '</script>';
}
...
paramGet('program名');
...
$user=$_GET[‘param’];
```
### 処理結果を戻す工夫
*  処理結果を自プログラムへ通知し、プログラムの上位に表示する　サンプル
  
```php
function branch($_page,$_param){
  echo '<html>';
  echo '<body onLoad="document.F.submit();">';
  echo "<form name='F' action={$_page} method='get'>";
  echo "<input type=hidden name=param value={$_param}>";
  echo '<input type="submit" name="next" value="Waiting...">';
  echo '</form>';
  exit();
}
...
$pgm="プログラム名";
if (isset($_GET['message'])){
  echo $_GET['message'];
  if ($_GET['message']=='error'){
    branch($pgm,'異常発生');
  }elseif($_GET['message']=='normal'){
    branch($pgm,'正常終了');
  }<br>
}
echo '<body>'; 
if (isset($_GET['param'])){  
  echo "<h3>{$_GET['param']}</h3><hr>";
}
echo '<h2>タイトル</h2>';
echo 'normalまたはerrorを入力し実行する';
echo '<form action="TestMsgTop.php" method="get">';
echo '<input type="text" name="message" value="">';
echo '<input type="hidden" name="param" value="入力なし">';
echo '<input type="submit" name="end" value="実行" >';
echo '</body></html>';
```
*  サンプル動作確認図
    
![動作確認１](http://ossiansunny.github.io/returnmsg1.png/150)

![動作確認２](returnmsg2.png/150)

![動作確認３](http://github.com/ossiansunny/ossiansunny/github.io/returnmsg3.png/150)

### ソースの場所、その他
*  ソースの場所は<a href="https://github.com/ossiansunny/visualmonitor">こちら</a>です<br>
*  おことわり  
不具合対応、インストール方法など、お手伝い出来ません、ご了承下さい




