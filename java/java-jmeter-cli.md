# JMeter（コマンドライン）
- [JMeterコマンドラインオプション一覧](https://jmeter.apache.org/usermanual/get-started.html#options){:target="_blank"}

## JMeterのインストール
```shell
# Mac
brew install jmeter

# Windows
TODO
```

## JMeterレポートの出力
```shell
# -n --nongui CLIモードで起動 -t --testfile JMeterプロファイルを指定
# -l --logfile jtlファイルを出力 -e HTMLレポートを作成 -o HTMLレポートの出力先を指定
jmeter -n -t pmacho.jmx -l pmacho.jtl -e -o ./report
```

複数回起動できるようにjtlファイルとHTMLレポートの出力先を起動ごとに作成すると良い。
```shell
current_datetime=$(date +%Y%m%d%H%M%S)
result_dir=./result-$current_datetime
mkdir $result_dir
report_dir=./$result_dir/report
jtl_file=./$result_dir/pmacho.jtl

jmeter -n -t pmacho.jmx -l $jtl_file -e -o $report_dir
```

## JMeterによるパフォーマンス試験の実施
スレッドを線形に上昇させてパフォーマンスの変化を確認する。HTMLレポートが非常に便利。

![java-jmeter-cli-606cc4bf20e1.png](https://programacho.blob.core.windows.net/images/java-jmeter-cli-606cc4bf20e1.png)

以下はレスポンスタイムを1秒に設定した特に問題のないアプリケーションの例。

1. スレッドは線形に上昇している。
![java-jmeter-cli-803a2275de01.png](https://programacho.blob.core.windows.net/images/java-jmeter-cli-803a2275de01.png)

2. TPSもスレッドと連動して線形に上昇している。
![java-jmeter-cli-438c34aed13b.png](https://programacho.blob.core.windows.net/images/java-jmeter-cli-438c34aed13b.png)

3. レスポンスタイムはスレッドの上昇に関わらず安定している。
![java-jmeter-cli-545898ed1b11.png](https://programacho.blob.core.windows.net/images/java-jmeter-cli-545898ed1b11.png)

## jtlファイルをGUIで閲覧
jtlファイルはJMeter GUIのSummary Reportで閲覧することができる。  
Summary Report上部のBrowseボタンでjtlファイルを指定する。

TODO: スクリーンショット

## jtlファイルからHTMLレポートを作成
```shell
# -g --reportonly jtlファイルを指定 -o HTMLレポートの出力先を指定
jmeter -g pmacho.jtl -o ./report
```

## JMeterを起動するJavaバイナリの変更
JMeterが使用するJavaバイナリはJM_LAUNCH環境変数を参照する。

```
# Mac
JM_LAUNCH=/Users/hainet50b/.jenv/versions/17.0.7/bin/java

# Mac with jEnv
JM_LAUNCH=$(jenv which java)

# Windows
$env:JM_LAUNCH="C:\Users\htakano\scoop\apps\openjdk17\current\bin\java.exe"
```
