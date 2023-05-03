# Java List Interface

## Listの要素を一定件数ごとに処理する
サンプルコード：[PartitionIntoGroups.java | GitHub](https://github.com/hainet50b/java-gym/blob/main/java-list-interface/src/main/java/org/example/PartitionIntoGroups.java){:target="_blank"}

`List<Object>`を`List<List<Object>>`に書き下す手段もあるが、  
割り切って`Iterator`に落としてforiを活用する方が見通しが良い場合がある。

```java
public static void main(String[] args) {
    List<Integer> list = IntStream.range(0, 90).boxed().toList();

    int blockSize = 20;
    int blockNumber = (list.size() / blockSize) + 1;

    Iterator<Integer> iterator = list.iterator();
    for (int i = 0; i < blockNumber; i++) {
        List<Integer> internalList = new ArrayList<>();

        for (int j = 0; j < blockSize; j++) {
            if (iterator.hasNext()) {
                internalList.add(iterator.next());
            }
        }

        System.out.println(String.join(",", internalList.stream()
                .map(v -> String.format("%02d", v))
                .toList()
        ));
    }
}
```