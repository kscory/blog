---
layout: post
comments: true
title:  "Keys Credentials and Storage"
date:   2018-07-09
author: Cory
categories: Android
permalink : /dev/android/keys_credentials_storage
tags: android security
---

> 이 포스팅은  Collin Stuart 께서 작성하신 [Keys Credentials and Storage](https://code.tutsplus.com/tutorials/keys-credentials-and-storage-on-android--cms-30827) 를 번역한 글입니다. 문제시 삭제하도록 하겠습니다.

# Keys, Credentials and Storage on Android

이번 글은 인증정보와, KeyStore에 대해서 이야기할 것입니다. 여기서 account 인증정보에 대해 소개하고, KeyStore를 사용하면서 데이터를 보호하는 방법을 소개하겠습니다.

종종 제 3자 서비스를 사용하게 될 때, ID / Password 로 로그인을 하는 경우와 같이 특정한 인증 형태가 필요할 때가 있습니다.

이런 경우 보통 우리는 사용자가 로그인 할 수 있는 UI 를 제공하고 이를 이용하여 로그인 인증 정보를 저장하는 형태를 띄게 됩니다. 그러나 어플리케이션은 제3의 계정을 위한 인증정보를 알아야만 할 필요가 없기 때문에 이는 가장 좋은 방법은 아닙니다. 대신에 우리는 이러한 민감한 정보들을 Account Manager 가 대신해서 처리하도록 설정할 수 있습니다.

## Account Manager

Account Manager 는 사용자가 온라인 계정의 센터 레지스트리에 접속할 수 있게 해줍니다. 그렇기 때문에 앱은 패스워드를 직접적으로 처리할 필요가 없습니다. 즉, Account Manager 는 ID/Password 대신해서 OAuth2 토큰과 같은 서비스를 위한 인증된 요청값을 제공해줍니다.

가끔, 필요한 모든 정보들은 이미 디바이스에 저장되어 있고, Account Manager 가 토큰을 갱신(refreshed) 하기 위해 서버에 요청할 필요가 있을 수도 있습니다.

다양한 앱에 대한 디바이스 설정에서 Account 섹션을 보셨을 지도 보릅니다. 만약 이용 가능한 계정의 목록들을 보고 싶다면 아래와 같이 작성하면 된다.

```java
AccountManager accountManager = AccountManager.get(this); // this 는 context 를 포함해야 한다.
Account[] accounts = accountManager.getAccounts();
```

이 코드는 ndroid.permission.GET_ACCOUNTS 의 퍼미션이 필요하다. 만약 특정한 계정을 찾고 싶다면 아래와 같이 작성할 수 있다.

```java
AccountManager accountManager = AccountManager.get(this);
Account[] accounts = accountManager.getAccountsByType("com.google");
```

한번 계정을 만들었다면, 계정에 대한 토큰은 `getAuthToken(Account, String, Bundle, Activity, AccountManagerCallback, Handler)` 메서드로 요청할 수 있습니다. 그런 다음 토큰을 사용하여 인증 된 API 요청을 서비스에 적용 할 수 있습니다. 이 API 요청은 HTTPS 요청 중에 사용자의 private 계정 세부 정보를 알 필요없이 토큰 파라미터를 전달하는 RESTful API 로 적용할 수 있습니다.

각각의 서비스들은 계정에 대한 승인 정보를 인증하고, 저장하는 방법이 다르기 때문에, Account Manager는 여러 계정 타입들을 위한 authenticator 모듈을 제공합니다. 물론, 안드로이드에는 많은 유명한 서비스들이 구현되어 있지만 앱에 대한 계정 인증 정보와 인증서에 대한 저장소를 처리하기 위해 고유한 authenticator 를 작성할 수도 있습니다. 이것들은 인증서가 암호화 되었는지 확인시켜 줄 수 있습니다. 여기서 Account Manager에서 여러 서비스에서 사용되는 인증서는 일반 텍스트로 저장되어 루팅한 폰을 가진 사용자에게 표시 될 수 있다는 것을 명심해야 합니다.

유지해야 할 필요가 있는 인증서 파일을 제 3자가 보내는 경우와 같이 간단한 자격 증명 대신 개인 또는 엔티티에 대한 키 또는 인증서를 처리해야 할 때가 있습니다.

다음 단계에서 인증 및 보안 통신을 위한 인증서를 사용하는 방법을 찾아볼 것이지만, 일단, 그러한 인증서를 저장하는 방법에 대해 설명할 것입니다. Keychain API는 원래 PKCS#12 파일의 개인 키 또는 인증서 쌍을 설치하는 매우 특정한 용도로 만들어졌습니다.

## Keychain

Android 4.0 (API 레벨 14)에 도입 된 Keychain API는 키 관리를 처리합니다. 특히 PrivateKey 및 X509Certificate 객체와 함께 작동하며 앱의 데이터 저장소를 사용하는 것보다 안전한 저장소를 제공합니다. 왜냐하면 개인 키에 대한 권한은 오직 사용자가 인증한 ​​앱만이 키에 액세스 할 수 있기 때문입니다. 즉, 자격증 명 저장소를 사용하려면 장치에 잠금 화면이 설정되어 있어야합니다. 또한 보안 하드웨어를 사용 가능할 수 있는 디바이스의 경우 키 체인의 객체를 저장할 수 있습니다.

인증서를 설치하는 코드는 다음과 같습니다.

```java
Intent intent = KeyChain.createInstallIntent();
byte[] p12Bytes = //... read from file, such as example.pfx or example.p12...
intent.putExtra(KeyChain.EXTRA_PKCS12, p12Bytes);
startActivity(intent)
```

사용자에게는 개인 키에 접근하기 위한 암호화 인증서의 이름을 지정할 수 있는 옵션을 묻는 메시지를 표시하게 됩니다. 키를 가져오기 위해 아래 코드와 같이 설치된 키 목록에서 선택할 수 있는 UI 를 사용자에게 제공할 수 있습니다.

```java
KeyChain.choosePrivateKeyAlias(this, this, new String[]{"RSA"}, null, null, -1, null);
```

선택이 끝나면, 비공개 키 또는 인증서의 chain 에 직접적으로 접근할 있도록 콜백에 String alias(final 로 선언된 String) 이 반환됩니다.

```java
public class KeychainTest extends Activity implements ..., KeyChainAliasCallback
{
    //...

    @Override
    public void alias(final String alias)
    {
        Log.e("MyApp", "Alias is " + alias);

        try
        {
            PrivateKey privateKey = KeyChain.getPrivateKey(this, alias);
            X509Certificate[] certificateChain = KeyChain.getCertificateChain(this, alias);
        }
        catch ...
    }

    //...
}
```

위를 이용하여, 중요한(민감한) 데이터를 신뢰할 수 있는 스토리지에 저장할 수 있는 방법을 살펴보도록 하겠습니다.

## The KeyStore

앞에서는 사용자가 제공 한 암호를 통해 데이터를 보호하는 방법에 대해 알아보았습니다. 이러한 설정도 괜찮지만 앱 요구사항으로 인해 사용자는 매번 로그인하게 하면서 추가적인 패스워드(패스코드)를 기억해야 할 수 았습니다.

여기에 KeyStore API 가 사용될 수 있습니다. API 1 부터 KeyStore 는 WiFi 와 VPN 인증서를 저장하는 시스템으로 사용되어 왔습니다. 그 후 API 18 때부터는 앱에서 특수한 대칭키를 사용할 수 있도록 해주었고, API 23(Android M) 부터는 비대칭키를 저장할 수 있게 되었습니다. 이 API 이후부터는 더이상 민감한 정보를 직접적으로 저장하지 않고 이 키들을 이용해서 암호화된 정보로 만든 뒤 저장하는 방법을 사용할 수 있게 되었습니다.

KeyStore에 키를 저장하면, 키의 secret content 를 공개하지 않고 키를 조작할 수 있는 장점이 있습니다. 왜냐하면 키 데이터는 앱 공간에서 입력되지 않기 때문입니다.

키는 권한으로 부터 보호되기 때문에 우리가 만든 그 하나의 액에서만 그 키에 접근할 수 있고, 그 키들은 하드웨어적으로 분리된 공간에 있기 때문에 더욱 안전하게 보관될 수 있습니다. 즉, 이렇게 하면 장치에서 키를 추출하기 더 어려워지는 컨테이너가 생성된다고 생각할 수 있습니다.

### Generate a New Random Key

여기서는 사용자가 제공한 패스코드로 AES key를 생성하는 대신 KeyStore 에서 보호 될 수 있는 임의의 키를 자동으로 생성할 수 있습니다. 아는 단지, KeyGenerator 인스턴스를 이용하면 됩나다. 그리고 `getinstance` 의 provider 인자에 `"AndroidKeyStore"` 로 세팅해주면 됩니다.

```java
/Generate a key and store it in the KeyStore
final KeyGenerator keyGenerator = KeyGenerator.getInstance(KeyProperties.KEY_ALGORITHM_AES, "AndroidKeyStore");
final KeyGenParameterSpec keyGenParameterSpec = new KeyGenParameterSpec.Builder("MyKeyAlias",
        KeyProperties.PURPOSE_ENCRYPT | KeyProperties.PURPOSE_DECRYPT)
        .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
        .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
        //.setUserAuthenticationRequired(true) //requires lock screen, invalidated if lock screen is disabled
        //.setUserAuthenticationValidityDurationSeconds(120) //only available x seconds from password authentication. -1 requires finger print - every time
        .setRandomizedEncryptionRequired(true) //different ciphertext for same plaintext on each call
        .build();
keyGenerator.init(keyGenParameterSpec);
keyGenerator.generateKey();
```

여기서 중요하게 봐야 할 부분은 바로 `.setUserAuthenticationRequired(true)` 과 `.setUserAuthenticationValidityDurationSeconds(120)` 입니다. 이를 이용하기 위해서는 사용자가 인증할 때까지 잠금 화면을 설정하고 키를 잠궈놔야 합니다.

문서에서 `.setUserAuthenticationValidityDurationSeconds()` 의 경우 password 인증인 경우에는 특정 초 만 키를 이용할 수 있다는 것을 의미하며, 만약 "-1" 로 전달할 경우, 키에 접근 할때마다 지문 인증이 필요하다는 것을 의미합니다. `.setUserAuthenticationRequired(true)` 를 설정하게 되면, 사용자가 잠금 화면을 제거하거나 변경할 때 키를 취소하는 효과를 낼 수 있습니다.

암호화되지 않은 키를 암호화된 데이터와 함께 저장하는 것은 현관에 집 열쇠를 두는 것과 비슷하다고 볼 수 있습니다. 그렇기 때문에 이러한 옵션들은 디바이스가 손상된 경우 나머지 키를 보호하려고 시도하게 됩니다. 예를 들어, 디바이스의 오프라인 데이터 덩어리 일 수 있습니다. 디바이스에 대해 알려진 password 가 없으면 해당 데이터는 쓸모 없게 될 수 있습니다.

`.setRandomizedEncryptionRequired(true)` 옵션을 사용하면 무작위로 랜덤화(랜덤 IV 사용))하게 되어, 동일한 데이터가 두 번째 암호화 될 경우 암호화되는 결과값은 달라질 수 잇게 됩니다. 즉, 이는 공격자가 동일한 데이터에서 피딩을 기반으로 암호문에 대한 단서를 얻을 수 없게 됩니다.

또 다른 옵션 중에 `setUserAuthenticationValidWhileOnBody(boolean remainsValid)` 가 있는데, 이는 장치가 더 이상 사람에게 있지 않음을 감지하면 키를 자동으로 잠그는 역할을 하게 됩니다.

### Encrypting Data

이제 key 가 KeyStore 에 저장되었으므로 SecretKey 가 주어진 경우, Cipher 객체를 사용하여 데이터를 암호화하는 메서드를 만들 수 있습니다. 이는 암호화된 데이터를 포함하는 HashMap 과 데이터를 해독하는데 필요한 랜덤값(랜덤 IV)을 반환합니다. IV 와 함께 암호화된 데이터는 파일이나 shared preferences 에 저장할 수 있습니다.

```java
private HashMap<String, byte[]> encrypt(final byte[] decryptedBytes)
{
    final HashMap<String, byte[]> map = new HashMap<String, byte[]>();
    try
    {
        //Get the key
        final KeyStore keyStore = KeyStore.getInstance("AndroidKeyStore");
        keyStore.load(null);
        final KeyStore.SecretKeyEntry secretKeyEntry = (KeyStore.SecretKeyEntry)keyStore.getEntry("MyKeyAlias", null);
        final SecretKey secretKey = secretKeyEntry.getSecretKey();

        //Encrypt data
        final Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
        cipher.init(Cipher.ENCRYPT_MODE, secretKey);
        final byte[] ivBytes = cipher.getIV();
        final byte[] encryptedBytes = cipher.doFinal(decryptedBytes);
        map.put("iv", ivBytes);
        map.put("encrypted", encryptedBytes);
    }
    catch (Throwable e)
    {
        e.printStackTrace();
    }

    return map;
}
```

### Decrypting to a Byte Array

복호화하기 위해서는 반대의 작업이 적용됩니다. Cipher 객체를 `DECRYPT_MODE` 상수를 이용하여 초기화하고 이를 복호화하면 byte[] 배열이 반환됩니다.

```java
private byte[] decrypt(final HashMap<String, byte[]> map)
{
    byte[] decryptedBytes = null;
    try
    {
        //Get the key
        final KeyStore keyStore = KeyStore.getInstance("AndroidKeyStore");
        keyStore.load(null);
        final KeyStore.SecretKeyEntry secretKeyEntry = (KeyStore.SecretKeyEntry)keyStore.getEntry("MyKeyAlias", null);
        final SecretKey secretKey = secretKeyEntry.getSecretKey();

        //Extract info from map
        final byte[] encryptedBytes = map.get("encrypted");
        final byte[] ivBytes = map.get("iv");

        //Decrypt data
        final Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
        final GCMParameterSpec spec = new GCMParameterSpec(128, ivBytes);
        cipher.init(Cipher.DECRYPT_MODE, secretKey, spec);
        decryptedBytes = cipher.doFinal(encryptedBytes);
    }
    catch (Throwable e)
    {
        e.printStackTrace();
    }

    return decryptedBytes;
}
```

### Testing the Example

테스트 해보죠!!!

```java
@TargetApi(Build.VERSION_CODES.M)
private void testEncryption()
{
    try
    {
        //Generate a key and store it in the KeyStore
        final KeyGenerator keyGenerator = KeyGenerator.getInstance(KeyProperties.KEY_ALGORITHM_AES, "AndroidKeyStore");
        final KeyGenParameterSpec keyGenParameterSpec = new KeyGenParameterSpec.Builder("MyKeyAlias",
                KeyProperties.PURPOSE_ENCRYPT | KeyProperties.PURPOSE_DECRYPT)
                .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
                .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
                //.setUserAuthenticationRequired(true) //requires lock screen, invalidated if lock screen is disabled
                //.setUserAuthenticationValidityDurationSeconds(120) //only available x seconds from password authentication. -1 requires finger print - every time
                .setRandomizedEncryptionRequired(true) //different ciphertext for same plaintext on each call
                .build();
        keyGenerator.init(keyGenParameterSpec);
        keyGenerator.generateKey();

        //Test
        final HashMap<String, byte[]> map = encrypt("My very sensitive string!".getBytes("UTF-8"));
        final byte[] decryptedBytes = decrypt(map);
        final String decryptedString = new String(decryptedBytes, "UTF-8");
        Log.e("MyApp", "The decrypted string is " + decryptedString);
    }
    catch (Throwable e)
    {
        e.printStackTrace();
    }
}
```
