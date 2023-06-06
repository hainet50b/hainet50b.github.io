# Spring Scheduling Tasks
{:.no_toc}

* toc
{:toc}

公式ドキュメント：[Scheduling Tasks](https://spring.io/guides/gs/scheduling-tasks/){:target="_blank"} ([日本語版](https://spring.pleiades.io/guides/gs/scheduling-tasks/){:target="_blank"})

## Scheduling Tasksを有効化
```java
@Configuration
@EnableScheduling
public class SchedulingTasksConfig {
}
```

プロファイルで有効・無効を切り替える場合は`@ConditionalOnProperty`を活用する。
```java
@Configuration
@EnableScheduling
@ConditionalOnProperty(name = "app.scheduling-tasks.enabled")
public class SchedulingTasksConfig {
}
```

## 定期実行タスクの設定
```java
@Component
public class ProgramachoScheduler {

    @Scheduled(cron = "0 0 * * * *")
    public void programacho() {
        // Be programacho!
    }
}
```

スケジュールの設定はプロパティを参照できる。
```java
@Scheduled(cron = "${app.scheduling-tasks.cron}")
```

## スレッド数の変更
デフォルトではシングルスレッドで稼働する。

```yaml
spring:
  task:
    scheduling:
      pool:
        size: 10
```
