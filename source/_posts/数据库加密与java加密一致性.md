---
title: 数据库加密与java加密一致性
date: 2019-10-22 17:49:09
tags:
  - JAVA
  - Postgresql
  - AES
  - 加密encrypt
  - 解密decrypt
---

> 本篇博主主要根据红宝书里讲到的JSON大家不常用到的功能，但是很实用的功能做介绍。

## 栏目
+ 目的
+ JAVA加密/解密具类
+ Postgresql加密解密
+ Postgresql加密解密函数

---

### 一、目的

为了安全起见，项目中要对用户的敏感信息（手机号，邮箱，护照号，社保卡号，身份证号，银行卡号）进行加密处理。那么就需要一套加密解密算法，并保证服务器端和数据库端加密解密的一致性。

### 二、JAVA加密/解密工具类


``` bash
public class AesUtil {

    private static final String ALGORITHM_MODE_PADDING5 = "AES/ECB/PKCS5Padding"; //support 16 length key
    private final static String KEY = "0000000000888888";//at least 16 characters

    
    /**
     * AES加密字符串
     * @param content
     * @return 密文
     */
    public static String encrypt(String content) {
        try {
            byte[] byteContent = content.getBytes("utf-8");
            SecretKeySpec skeySpec = new SecretKeySpec(KEY.getBytes("UTF-8"), "AES");// 转换为AES专用密钥
            Cipher cipher = Cipher.getInstance(ALGORITHM_MODE_PADDING5);
            cipher.init(Cipher.ENCRYPT_MODE, skeySpec);
            byte[] resultByter = cipher.doFinal(byteContent);// 加密
            String result = encodeBase64(resultByter);
            return result;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 解密AES加密过的字符串
     * @param content
     * @return 明文
     */
    public static String  decrypt(String  content) {
        try {
            byte[] contentBytes = decodeBase64(content);
            SecretKeySpec skeySpec = new SecretKeySpec(KEY.getBytes("UTF-8"), "AES");
            Cipher cipher = Cipher.getInstance(ALGORITHM_MODE_PADDING5);
            cipher.init(Cipher.DECRYPT_MODE, skeySpec);
            String result = new String(cipher.doFinal(contentBytes),"UTF-8");
            return result; // 明文

        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }


    public static byte[] decodeBase64(String encodedText)
    {
        final java.util.Base64.Decoder decoder = java.util.Base64.getDecoder();
        return decoder.decode(encodedText);
    }

    public static String encodeBase64(byte[] data)
    {
        final java.util.Base64.Encoder encoder = java.util.Base64.getEncoder();
        return encoder.encodeToString(data);
    }

    public static void main(String[] args) throws Exception{
        String content = "welcome to 明仔博客";
        String encryptStr =  AesUtil.encrypt(content);
        System.out.println("加密后：" + encryptStr);
        String decryptStr = AesUtil.decrypt(encryptStr);
        System.out.println("解密后的内容：" + decryptStr);
    }
}
``` 
运行结果如下图
![参数概览](/assets/img/aesresult.png)


### 二、Postgresql加密解密

首先需要在Postgresql数据库端安装pgcrypto模块, 为了使用decrypt和encrypt函数

create extension pgcrypto

SELECT encode(encrypt('welcome to 明仔博客'::bytea,'0000000000888888','aes-ecb'),'base64') 

<font color=#eb7350 size=2>值得注意的是秘钥和加密模式要一致,也可以通过创建数据库函数来简化加密过程</font>

运行结果如下图
![参数概览](/assets/img/postgresql.png)




