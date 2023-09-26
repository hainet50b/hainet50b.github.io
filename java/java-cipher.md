# Java 暗号化・復号（AES256, RSA）
{:.no_toc}

* toc
{:toc}

## AES256
```java
import javax.crypto.Cipher;
import javax.crypto.spec.IvParameterSpec;
import javax.crypto.spec.SecretKeySpec;
import java.security.GeneralSecurityException;
import java.util.Base64;

public class AES256Util {

    private static final String ALGORITHM = "AES";
    private static final String TRANSFORMATION = "AES/CBC/PKCS5Padding";

    public String encrypt(String value, String key, String iv) throws GeneralSecurityException {
        SecretKeySpec secretKeySpec = new SecretKeySpec(key.getBytes(), ALGORITHM);

        Cipher cipher = Cipher.getInstance(TRANSFORMATION);
        cipher.init(Cipher.ENCRYPT_MODE, secretKeySpec, new IvParameterSpec(iv.getBytes()));
        byte[] encryptedValue = cipher.doFinal(value.getBytes());

        return Base64.getEncoder().encodeToString(encryptedValue);
    }

    public String decrypt(String value, String key, String iv) throws GeneralSecurityException {
        SecretKeySpec secretKeySpec = new SecretKeySpec(key.getBytes(), ALGORITHM);

        Cipher cipher = Cipher.getInstance(TRANSFORMATION);
        cipher.init(Cipher.DECRYPT_MODE, secretKeySpec, new IvParameterSpec(iv.getBytes()));
        byte[] decryptedValue = cipher.doFinal(Base64.getDecoder().decode(value));

        return new String(decryptedValue);
    }
}
```

## RSA
事前にRSA暗号鍵ペアを作成してクラスパスに配置する。
```shell
openssl genpkey -algorithm RSA -out pmacho.pem -pkeyopt rsa_keygen_bits:2048
openssl rsa -pubout -in pmacho.pem -out pmacho-pub.pem

ls -la
-rw-r--r--@ 1 hainet50b  staff   451 Sep 26 19:42 pmacho-pub.pem
-rw-------@ 1 hainet50b  staff  1704 Sep 26 19:42 pmacho.pem
```

```java
import javax.crypto.Cipher;
import java.security.GeneralSecurityException;
import java.security.KeyFactory;
import java.security.PrivateKey;
import java.security.PublicKey;
import java.security.spec.PKCS8EncodedKeySpec;
import java.security.spec.X509EncodedKeySpec;
import java.util.Base64;

public class RSAUtil {

    private static final String ALGORITHM = "RSA";

    private static final String TRANSFORMATION = "RSA/ECB/PKCS1Padding";

    public String encrypt(String value, String publicKeyString) throws GeneralSecurityException {
        KeyFactory keyFactory = KeyFactory.getInstance(ALGORITHM);
        X509EncodedKeySpec keySpec = new X509EncodedKeySpec(Base64.getDecoder().decode(
                publicKeyString
                        .replace("-----BEGIN PUBLIC KEY-----", "")
                        .replace("-----END PUBLIC KEY-----", "")
                        .replaceAll("\\s", "")
        ));
        PublicKey publicKey = keyFactory.generatePublic(keySpec);

        Cipher cipher = Cipher.getInstance(TRANSFORMATION);
        cipher.init(Cipher.ENCRYPT_MODE, publicKey);
        byte[] encryptedValue = cipher.doFinal(value.getBytes());

        return Base64.getEncoder().encodeToString(encryptedValue);
    }

    public String decrypt(String value, String privateKeyString) throws GeneralSecurityException {
        KeyFactory keyFactory = KeyFactory.getInstance(ALGORITHM);
        PKCS8EncodedKeySpec keySpec = new PKCS8EncodedKeySpec(Base64.getDecoder().decode(
                privateKeyString
                        .replace("-----BEGIN PRIVATE KEY-----", "")
                        .replace("-----END PRIVATE KEY-----", "")
                        .replaceAll("\\s", "")
        ));
        PrivateKey privateKey = keyFactory.generatePrivate(keySpec);

        Cipher cipher = Cipher.getInstance(TRANSFORMATION);
        cipher.init(Cipher.DECRYPT_MODE, privateKey);
        byte[] decryptedValue = cipher.doFinal(Base64.getDecoder().decode(value.getBytes()));

        return new String(decryptedValue);
    }
}
```
