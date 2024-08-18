# java.security.InvalidKeyException: Illegal key size。

```
SecretKeySpec secretKeySpec = new SecretKeySpec(aesKey, "AES");
Cipher cipher = Cipher.getInstance("AES/CTR/NoPadding");
IvParameterSpec ips = createCtrIv(nonce);
cipher.init(1, secretKeySpec, ips);　　　
　//当代码运行到这一行时就报错了。爆出上面的异常
```

```代码
// 使用定时更新的签名验证器，不需要传入证书
            ScheduledUpdateCertificatesVerifier verifier = new ScheduledUpdateCertificatesVerifier(
                    wechatPay2Credentials,
                    sysPayConf.getWxPayKey().getBytes(StandardCharsets.UTF_8));
```

感到一脸懵逼，还好网络是万能的，百度一下，简单对比一下，就找到了解决方案。然后测试之后发现也是没有问题的。

异常原因：如果密钥大于128, 会抛出java.security.InvalidKeyException: Illegal key size 异常. 因为密钥长度是受限制的, java运行时环境读到的是受限的policy文件. 文件位于${java_home}/jre/lib/security, 这种限制是因为美国对软件出口的控制.

解决方案：去官方下载JCE无限制权限策略文件。

jdk 5: http://www.oracle.com/technetwork/java/javasebusiness/downloads/java-archive-downloads-java-plat-419418.html#jce_policy-1.5.0-oth-JPR

jdk6: http://www.oracle.com/technetwork/java/javase/downloads/jce-6-download-429243.html

JDK7的下载地址: [Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files 7 Download](http://www.oracle.com/technetwork/java/javase/downloads/jce-7-download-432124.html "Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files 7 Download")  
JDK8的下载地址: [JCE Unlimited Strength Jurisdiction Policy Files for JDK/JRE 8 Download](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html "JCE Unlimited Strength Jurisdiction Policy Files for JDK/JRE 8 Download") 

下载后解压，可以看到local_policy.jar和US_export_policy.jar以及readme.txt  
如果安装了JRE，将两个jar文件放到%JRE_HOME%\lib\security目录下覆盖原来的文件  
如果安装了JDK，还要将两个jar文件也放到%JDK_HOME%\jre\lib\security目录下覆盖原来文件。

然后DuangDuangDuangDuang，就ok了。
