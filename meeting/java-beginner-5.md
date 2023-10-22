# Java Beginner #5

## オブジェクト指向
例えばエアコンは電源・温度・運転から成り立つとする。  
これらの状態や操作をオブジェクトにまとめて互いに独立して定義することで取り扱いやすくする。

```java
// 電源
public class Power {

    private boolean on = false;

    public boolean isOn() {
        return this.on;
    }

    public void turnOn() {
        this.on = true;
    }

    public void turnOff() {
        this.on = false;
    }
}

// 温度
public class Temperature {

    private int target;

    public void setTarget(int target) {
        this.target = target;
    }

    public int getTarget() {
        return this.target;
    }

    public int getTemperature() {
        // 仮の温度を返却している。
        return 25;
    }
}

// 運転
public class Control {

    private Mode mode = Mode.COOL;

    public void setCoolMode() {
        System.out.println("COOLモードに設定します。");
        this.mode = Mode.COOL;
    }

    public void setKeepMode() {
        System.out.println("KEEPモードに設定します。");
        this.mode = Mode.KEEP;
    }

    public void setHeatMode() {
        System.out.println("HEATモードに設定します。");
        this.mode = Mode.HEAT;
    }

    public void adjust(int temperature, int target) {
        if (temperature == target) {
            setKeepMode();
        } else if (temperature > target) {
            setCoolMode();
        } else {
            setHeatMode();
        }
    }

    public enum Mode {
        COOL,
        KEEP,
        HEAT
    }
}

// エアコン
public class AirConditioner {

    public static void main(String[] args) throws InterruptedException {
        Power power = new Power();
        Temperature temperature = new Temperature();
        Control control = new Control();

        // リモコンから電源ONのイベントを受け付けて、目標温度を設定する。
        power.turnOn();
        temperature.setTarget(24);

        // リモコンの割り込みで電源がOFFになるまで動作し続ける。
        while (power.isOn()) {
            control.adjust(
            temperature.getTemperature(),
            temperature.getTarget()
            );

            TimeUnit.SECONDS.sleep(1);
        }
    }
}
```

電源ユニットに電気代測定機能を付与したモデルを開発する場合にはPowerを継承する。

```java
// 電気代測定機能を付与したモデル
public class FG204AC extends Power {

    private int cost;

    public void calculateCost() {
        this.cost = 5000;
    }

    public int getCost() {
        return cost;
    }
}

// Powerを差し替えて利用する。
public class AirConditioner {

    public static void main(String[] args) throws InterruptedException {
        // Powerの代わりにFG204ACを利用する。
        FG204AC power = new FG204AC();
        Temperature temperature = new Temperature();
        Control control = new Control();

        power.turnOn();
        temperature.setTarget(24);

        while (power.isOn()) {
            control.adjust(
            temperature.getTemperature(),
            temperature.getTarget()
            );

            // FG204AC固有の操作にアクセスする。
            power.calculateCost();
            System.out.printf("現在の電気代：%s\n", power.getCost());

            TimeUnit.SECONDS.sleep(1);
        }
    }
}
```

## ポリモーフィズム
インターフェースは同一であってもオブジェクトごとに動作が異なること。

```java
// Animalはsayメソッドで声を出すものであると定義する。
public interface Animal {
    void say();
}

// Dogはsayメソッドで「わんわん」と鳴く。
public class Dog implements Animal {
    @Override
    public void say() {
        System.out.println("わんわん");
    }
}

// Catはsayメソッドで「にゃー」と鳴く。
public class Cat implements Animal {
    @Override
    public void say() {
        System.out.println("にゃー");
    }
}
```
