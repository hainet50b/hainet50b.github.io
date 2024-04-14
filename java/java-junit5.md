# JUnit 5
{:.no_toc}

* toc
{:toc}

## パラメータ化テストの活用例
以下の秋葉原電気街で歩行者天国が開催中であるかを確かめるメソッドをSUTとする。

```java
public class AkihabaraPedestrianParadiseService {

    public boolean isInPedestrianParadise(DayOfWeek week, LocalTime time, boolean sunny) {
        return week.equals(DayOfWeek.SUNDAY)
                && time.getHour() >= 13
                && time.getHour() < 18
                && sunny;
    }
}
```

曜日、時間、天候の3点の組み合わせをテストする。

```java
class AkihabaraPedestrianParadiseServiceTest {

    @ParameterizedTest
    @MethodSource("validWeeksAndTimes")
    void 秋葉原電気街の歩行者天国が開催中である(DayOfWeek week, LocalTime time, boolean sunny) {
        AkihabaraPedestrianParadiseService sut = new AkihabaraPedestrianParadiseService();

        boolean actual = sut.isInPedestrianParadise(week, time, sunny);

        Assertions.assertTrue(actual);
    }

    @ParameterizedTest
    @MethodSource("invalidWeeksAndTimes")
    void 秋葉原電気街の歩行者天国が開催中でない(DayOfWeek week, LocalTime time, boolean sunny) {
        AkihabaraPedestrianParadiseService sut = new AkihabaraPedestrianParadiseService();

        boolean actual = sut.isInPedestrianParadise(week, time, sunny);

        Assertions.assertFalse(actual);
    }

    static Stream<Arguments> validWeeksAndTimes() {
        return Stream.of(
                arguments(DayOfWeek.SUNDAY, LocalTime.of(13, 0), true),
                arguments(DayOfWeek.SUNDAY, LocalTime.of(17, 59), true)
        );
    }

    static Stream<Arguments> invalidWeeksAndTimes() {
        return Stream.of(
                arguments(DayOfWeek.MONDAY, LocalTime.of(13, 0), true),
                arguments(DayOfWeek.SUNDAY, LocalTime.of(18, 0), true),
                arguments(DayOfWeek.SUNDAY, LocalTime.of(13, 0), false),
                arguments(DayOfWeek.SUNDAY, LocalTime.of(12, 59), true),
                arguments(DayOfWeek.SUNDAY, LocalTime.of(18, 0), true)
        );
    }
}
```

## IntelliJ IDEAでJUnitテスト結果の日本語文字化けを解消する。
以下のように設定することで解消する。

```
Settings > Editor > File Encodings > Global Encoding: UTF-8
```

問題なく日本語を文字化けすることなく表示できる。

![java-junit5-display-japanese.png](https://programacho.blob.core.windows.net/images/java-junit5-display-japanese.png)
