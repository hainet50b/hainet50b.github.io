# Java Stream API
{:.no_toc}

* toc
{:toc}

## Comparator（sorted / min / max）
```java
List<String> values = List.of("foo", "bar", "baz");

// 匿名クラス
values.stream()
        .sorted(new Comparator<String>() {
            @Override
            public int compare(String v1, String v2) {
                return v1.compareTo(v2);
            }
        })
        .toList();

// ラムダ式
values.stream()
        .sorted((v1, v2) -> v1.compareTo(v2))
        .toList();

// メソッド参照（String#compareTo）
values.stream()
        .sorted(String::compareTo)
        .toList();

// 昇順
// public int compare(Comparable<Object> c1, Comparable<Object> c2) {
//     return c1.compareTo(c2);
// }
values.stream()
        .sorted(Comparator.naturalOrder())
        .toList();

// 逆順
// public int compare(Comparable<Object> c1, Comparable<Object> c2) {
//     return c2.compareTo(c1);
// }
values.stream()
        .sorted(Comparator.reverseOrder())
        .toList();

public record User(String name, Integer age) {
}

List<User> users = List.of(new User("haient50b", 31));

// オブジェクトの展開（ラムダ式）
users.stream()
        .sorted((u1, u2) -> u1.age().compareTo(u2.age()))
        .toList();

// オブジェクトの展開（Comparator#comparing）
// return (c1, c2) -> keyExtractor.apply(c1).compareTo(keyExtractor.apply(c2));
users.stream()
        .sorted(Comparator.comparing(u -> u.age()))
        .toList();

// オブジェクトの展開（Comparator#comparing / メソッド参照）
users.stream()
        .sorted(Comparator.comparing(User::age))
        .toList();

// オブジェクトの展開（Comparator#comparing / メソッド参照 / 逆順）
users.stream()
        .sorted(Comparator.comparing(User::age).reversed())
        .toList();

// Comparatorの合成
// return (Comparator<T> & Serializable) (c1, c2) -> {
//     int res = compare(c1, c2);
//     return (res != 0) ? res : other.compare(c1, c2);
// };
users.stream()
        .sorted(Comparator.comparing(User::age).thenComparing(User::name))
        .toList();
```

## Collectors
```java
List<String> values = List.of("foo", "bar", "baz");

// Stream#collect
// <R> R collect(Supplier<R> supplier,
//         BiConsumer<R, ? super T> accumulator,
//         BiConsumer<R, R> combiner)
values.stream()
        .collect(ArrayList::new, ArrayList::add, ArrayList::addAll);

// Collectors#toList
// return new CollectorImpl<>(ArrayList::new, List::add,
//         (left, right) -> { left.addAll(right); return left; },
//         CH_ID);
values.stream()
        .collect(Collectors.toList());

// Collectors#toSet
// return new CollectorImpl<>(HashSet::new, Set::add,
//         (left, right) -> {
//             if (left.size() < right.size()) {
//                 right.addAll(left); return right;
//             } else {
//                 left.addAll(right); return left;
//             }
//         },
//         CH_UNORDERED_ID);
values.stream()
        .collect(Collectors.toSet());

public record User(String name, Integer age) {
}

List<User> users = List.of(new User("haient50b", 31));

// グループ化
// Map<Integer, List<User>>
users.stream()
        .collect(Collectors.groupingBy(User::age));

// オブジェクトを展開してグループ化
// Map<Integer, List<String>>
users.stream()
        .collect(Collectors.groupingBy(User::age, Collectors.mapping(User::name, Collectors.toList())));

// 結果を畳み込んでグループ化
// Map<String, Optional<User>>
users.stream()
        .collect(Collectors.groupingBy(User::name, Collectors.reducing(BinaryOperator.maxBy(Comparator.comparing(User::age)))));
```