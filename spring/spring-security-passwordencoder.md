# Spring Security PasswordEncoder
{:.no_toc}

* toc
{:toc}

## PasswordEncoder実装一覧
実装一覧：[spring-security \| GitHub](https://github.com/spring-projects/spring-security/tree/main/crypto/src/main/java/org/springframework/security/crypto/password){:target="_blank"}

## DelegatingPasswordEncoder
TODO

## PasswordEncoder適用タイミング
PasswordEncoderは認証ヘッダーを含むHTTPリクエストを受け取るとエンドポイントに関わらずエンコードを実行する。  
デフォルトでBCryptPasswordEncoderによって2^10回のハッシュ計算が行われるためDoS脆弱性につながる。  
インメモリの固定パスワードを使用する場合など、差し支えのない範囲で強度を下げることを検討する。

以下、検証用の設定と検証結果を記述する。

依存関係
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

エンドポイント定義
```java
@RestController
public class AppRestController {

    @GetMapping("/foo")
    public String foo() {
        return "foo!";
    }

    @GetMapping("/bar")
    public String bar() {
        return "bar!";
    }

    // @GetMapping("/baz")
    public String baz() {
        return "baz!";
    }
}
```

セキュリティ設定
```java
@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers("/foo")
                        .authenticated()
                        .anyRequest()
                        .permitAll()
                )
                .httpBasic(Customizer.withDefaults())
                .build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new PasswordEncoder() {
            @Override
            public String encode(CharSequence rawPassword) {
                System.out.println("PasswordEncoder#encode is called.");
                return rawPassword.toString();
            }

            @Override
            public boolean matches(CharSequence rawPassword, String encodedPassword) {
                return rawPassword.equals(encodedPassword);
            }

            @Override
            public boolean upgradeEncoding(String encodedPassword) {
                return true;
            }
        };
    }
}
```

クレデンシャル設定
```yml
spring:
  security:
    user:
      name: user
      password: password
```

検証結果
| エンドポイント | 認証ヘッダー | PasswordEncoder | HTTPレスポンス |
| --- | --- | --- | --- |
| /foo | なし | 動作しない | 401 |
| /foo | あり | 動作する | 200 |
| /foo | あり（不正なクレデンシャル） | 動作する | 401 |
| /bar | なし | 動作しない | 200 |
| /bar | あり | 動作する | 200 |
| /bar | あり（不正なクレデンシャル） | 動作する | 401 |
| /baz | なし | 動作しない | 404 |
| /baz | あり | 動作する | 404 |
| /baz | あり（不正なクレデンシャル） | 動作する | 401 |
