# HTTPS在Android中的应用 {#https在android中的应用}

[http://android.jobbole.com/83787/](http://android.jobbole.com/83787/)

## 基本使用 {#基本使用}

访问百度首页：

```
    URL url = new URL("https://www.baidu.com");
    HttpsURLConnection conn = (HttpsURLConnection)url.openConnection();
    //InputStream is = conn.getInputStream();
    InputStream in = new BufferedInputStream(conn.getInputStream());
    ...

```

## 使用自签名报错 {#使用自签名报错}

12306使用的是“自签名”证书，所以，我们在Android中直接访问[https://kyfw.12306.cn/otn/regist/init](https://kyfw.12306.cn/otn/regist/init)时，会得到如下异常：

```
    java.security.cert.CertPathValidatorException: Trust anchor for certification path not found.

```

## 不对证书做检查 {#不对证书做检查}

客户端不对证书做任何检查，这是一种有很大安全漏洞的办法，如下所示：

![](https://frankyzj.gitbooks.io/android/content/assets/%E5%BF%BD%E7%95%A5%E8%AF%81%E4%B9%A6%E6%A3%80%E6%9F%A5%E7%9A%84%E6%96%B9%E6%B3%95.png)

```
import org.apache.http.conn.ssl.SSLSocketFactory;
public class MySSLSocketFactory extends SSLSocketFactory {
    SSLContext sslContext = SSLContext.getInstance("TLS");

    public MySSLSocketFactory(KeyStore truststore) throws NoSuchAlgorithmException, KeyManagementException, KeyStoreException, UnrecoverableKeyException {
        super(truststore);

        TrustManager tm = new X509TrustManager() {
            public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {
            }

            public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {
            }

            public X509Certificate[] getAcceptedIssuers() {
                return null;
            }
        };

        sslContext.init(null, new TrustManager[] { tm }, null);
   }

    @Override
    public Socket createSocket(Socket socket, String host, int port, boolean autoClose) throws IOException, UnknownHostException {
        return sslContext.getSocketFactory().createSocket(socket, host, port, autoClose);
    }

    @Override
    public Socket createSocket() throws IOException {
        return sslContext.getSocketFactory().createSocket();
    }
}

```

首先定义一个自己的SSLTrustAllManager，其继承自X509TrustManager，具体来说，用SSLTrustAllManager创建一个SSLContext，然后用SSLContext生成一个SSLSocketFactory，然后通过调用HttpURLConnectoin的setSSLSocketFactory方法将其给某个具体的连接对象使用。由于SSLTrustAllManager没有对其中的三个核心方法进行具体实现，也就是不对证书做任何审查。这样无论服务器的证书如何，都能建立HTTPS连接，因为客户端直接忽略了验证服务器证书这一步。

## 正确姿势 {#正确姿势}

解决此问题的办法是让Android客户端信任12306的证书，而不是不对证书做任何检查。

我们通过上面提到的方法得到12306的证书12306.cer，将其放到assets目录下，也就是将12306.cer打包到我们的App中，然后用如下代码访问12306的HTTPS站点。

```
class DownloadThread extends Thread{
        @Override
        public void run() {
            HttpsURLConnection conn = null;
            InputStream is = null;
            try {
                URL url = new URL("https://kyfw.12306.cn/otn/regist/init");
                conn = (HttpsURLConnection)url.openConnection();

                //创建X.509格式的CertificateFactory
                CertificateFactory cf = CertificateFactory.getInstance("X.509");
                //从asserts中获取证书的流
                InputStream cerInputStream = getAssets().open("12306.cer");//SRCA.cer
                //ca是java.security.cert.Certificate，不是java.security.Certificate，
                //也不是javax.security.cert.Certificate
                Certificate ca;
                try {
                    //证书工厂根据证书文件的流生成证书Certificate
                    ca = cf.generateCertificate(cerInputStream);
                    System.out.println("ca=" + ((X509Certificate) ca).getSubjectDN());
                } finally {
                    cerInputStream.close();
                }

                // 创建一个默认类型的KeyStore，存储我们信任的证书
                String keyStoreType = KeyStore.getDefaultType();
                KeyStore keyStore = KeyStore.getInstance(keyStoreType);
                keyStore.load(null, null);
                //将证书ca作为信任的证书放入到keyStore中
                keyStore.setCertificateEntry("ca", ca);

                //TrustManagerFactory是用于生成TrustManager的，我们创建一个默认类型的TrustManagerFactory
                String tmfAlgorithm = TrustManagerFactory.getDefaultAlgorithm();
                TrustManagerFactory tmf = TrustManagerFactory.getInstance(tmfAlgorithm);
                //用我们之前的keyStore实例初始化TrustManagerFactory，这样tmf就会信任keyStore中的证书
                tmf.init(keyStore);
                //通过tmf获取TrustManager数组，TrustManager也会信任keyStore中的证书
                TrustManager[] trustManagers = tmf.getTrustManagers();

                //创建TLS类型的SSLContext对象， that uses our TrustManager
                SSLContext sslContext = SSLContext.getInstance("TLS");
                //用上面得到的trustManagers初始化SSLContext，这样sslContext就会信任keyStore中的证书
                sslContext.init(null, trustManagers, null);

                //通过sslContext获取SSLSocketFactory对象
                SSLSocketFactory sslSocketFactory = sslContext.getSocketFactory();
                //将sslSocketFactory通过setSSLSocketFactory方法作用于HttpsURLConnection对象
                //这样conn对象就会信任我们之前得到的证书对象
                conn.setSSLSocketFactory(sslSocketFactory);

                is = conn.getInputStream();
                //将得到的InputStream转换为字符串
                final String str = getStringByInputStream(is);
                handler.post(new Runnable() {
                    @Override
                    public void run() {
                        textView.setText(str);
                        btn.setEnabled(true);
                    }
                });
            }catch (Exception e){
                e.printStackTrace();
                final String errMessage = e.getMessage();
                handler.post(new Runnable() {
                    @Override
                    public void run() {
                        btn.setEnabled(true);
                        Toast.makeText(MainActivity.this, errMessage, Toast.LENGTH_LONG).show();
                    }
                });
            }finally {
                if(conn != null){
                    conn.disconnect();
                }
            }
        }

```

1. 首先从asserts目录中获取12306.cer证书的文件流，然后用CertificateFactory的generateCertificate方法将该文件流转化为一个证书对象Certificate，该证书就是12306网站的数字证书。
2. 创建一个默认类型的KeyStore实例，keyStore用于存储着我们信赖的数字证书，将12306的数字证书放入keyStore中。
3. 我们获取一个默认的TrustManagerFactory的实例，并用之前的keyStore初始化它，这样TrustManagerFactory的实例也会信任keyStore中12306.cer证书。通过TrustManagerFactory的getTrustManagers方法获取TrustManager数组，该数组中的TrustManager也会信任12306.cer证书。
4. 创建一个TLS类型的SSLContext实例，并用之前的TrustManager数组初始化sslContext实例，这样该sslContext实例也会信任12306.cer证书。
5. 通过sslContext获取SSLSocketFactory对象，将sslSocketFactory通过setSSLSocketFactory方法作用于HttpsURLConnection对象，这样conn对象就会信任keyStore中的12306.cer证书。这样一来，客户端就会信任12306的证书，从而正确建立HTTPS连接。

以上的处理过程是Google官方建议的流程，步骤流程总结如下：

证书文件流 InputStream -&gt; Certificate -&gt; KeyStore -&gt; TrustManagerFactory -&gt; TrustManager\[\] -&gt; SSLContext -&gt; SSLSocketFactory -&gt; HttpsURLConnection

以上步骤的起点是获取证书文件的文件流，不一定非要从assets目录中获取，也可以通过其他途径得到，只要能拿到证书的文件流即可。

## 使用自签名证书 {#使用自签名证书}

直接在代码中固定写死使用某个服务器的证书. 然后在应用中使用自己定义的信任存储\(trust store\)代替手机系统自带的那个, 去连接指定的服务器.

这样做的好处是, 我们既能使用自签名证书, 又不需要额外安装其他证书.

##### 好处： {#好处：}

1. **安全性提升**
   - 采用这种方式, 应用不再依赖系统自带的信任存储\(trust store\). 使得破解这种应用变得复杂: 首先你要反编译, 修改完后, 还要重新编译. 关键是你不可能使用应用作者原先用的那个 keystore 文件重新颁发证书。
2. **成本降低**
   - 证书锁定方式让我们可以在自己的服务器上使用免费的自签名证书, 调用自己写的 API. 虽说复杂了点, 可是像这种既不花钱, 还能提高应用安全的好事上哪找去?

##### 劣势： {#劣势：}

**适应性较差**- 一旦 SSL 证书出现变动, 应用也要跟着升级. 再发布到 Google Play. 然后祈祷用户能都升级到最新版本.

