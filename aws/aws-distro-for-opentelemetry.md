# AWS Distro for OpenTelemetry

## OpenTelemetry Collector/Exporterの構築
以下はEC2上での動作を前提としている。

```shell
# RPMパッケージをダウンロード
# 公式ドキュメントではソースコードをビルドする手順が紹介されている。
# しかし処理時間とストレージを大量に消費するためパッケージをダウンロードする手順が良い。
wget https://aws-otel-collector.s3.amazonaws.com/amazon_linux/amd64/latest/aws-otel-collector.rpm

# RPMパッケージのインストール
sudo rpm -Uvh  ./aws-otel-collector.rpm
```

## OpenTelemetry Collector/Exporterの確認・開始・停止
```shell
# 確認
sudo /opt/aws/aws-otel-collector/bin/aws-otel-collector-ctl -a status

# 開始
sudo /opt/aws/aws-otel-collector/bin/aws-otel-collector-ctl -a start

# 停止
sudo /opt/aws/aws-otel-collector/bin/aws-otel-collector-ctl -a stop
```

# 設定ファイル
初期設定では`/opt/aws/aws-otel-collector/etc/config.yml`が読み込まれる。  
初回起動時に以下の内容でファイルが自動生成されている。

```yaml
extensions:
  health_check:

receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
  awsxray:
    endpoint: 0.0.0.0:2000
    transport: udp

processors:
  batch/traces:
    timeout: 1s
    send_batch_size: 50
  batch/metrics:
    timeout: 60s

exporters:
  awsxray:
  awsemf:

service:
  pipelines:
    traces:
      receivers: [otlp,awsxray]
      processors: [batch/traces]
      exporters: [awsxray]
    metrics:
      receivers: [otlp]
      processors: [batch/metrics]
      exporters: [awsemf]

  extensions: [health_check]
```

起動するとポート4318でHTTPエンドポイントの待ち受けが開始する。

```shell
curl localhost:4318/v1/metrics

405 method not allowed, supported: [POST]
```