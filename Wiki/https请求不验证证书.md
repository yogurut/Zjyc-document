```java
CloseableHttpClient httpclient = getHttpsClient();
/**
     * 获取https连接（不验证证书）
     *
     * @return
     */
    private static CloseableHttpClient getHttpsClient() {
        RegistryBuilder<ConnectionSocketFactory> registryBuilder = RegistryBuilder.<ConnectionSocketFactory>create();
        ConnectionSocketFactory plainSF = new PlainConnectionSocketFactory();
        registryBuilder.register("http", plainSF);
        // 指定信任密钥存储对象和连接套接字工厂
        try {
            KeyStore trustStore = KeyStore.getInstance(KeyStore.getDefaultType());
            // 信任任何链接
            TrustStrategy anyTrustStrategy = new TrustStrategy() {

                @Override
                public boolean isTrusted(java.security.cert.X509Certificate[] arg0, String arg1) throws java.security.cert.CertificateException {
                    // TODO Auto-generated method stub
                    return true;
                }
            };
            SSLContext sslContext = SSLContexts.custom().useTLS().loadTrustMaterial(trustStore, anyTrustStrategy).build();
            LayeredConnectionSocketFactory sslSF = new SSLConnectionSocketFactory(sslContext, SSLConnectionSocketFactory.ALLOW_ALL_HOSTNAME_VERIFIER);
            registryBuilder.register("https", sslSF);
        } catch (KeyStoreException e) {
            throw new RuntimeException(e);
        } catch (KeyManagementException e) {
            throw new RuntimeException(e);
        } catch (NoSuchAlgorithmException e) {
            throw new RuntimeException(e);
        }
        Registry<ConnectionSocketFactory> registry = registryBuilder.build();
        // 设置连接管理器
        PoolingHttpClientConnectionManager connManager = new PoolingHttpClientConnectionManager(registry);
        // 构建客户端
        return HttpClientBuilder.create().setConnectionManager(connManager).build();
    }
```
# 系统中httpsUtil类
```java
    package com.zjyc.scm.util;

import org.apache.http.HttpEntity;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.config.Registry;
import org.apache.http.config.RegistryBuilder;
import org.apache.http.conn.socket.ConnectionSocketFactory;
import org.apache.http.conn.socket.LayeredConnectionSocketFactory;
import org.apache.http.conn.socket.PlainConnectionSocketFactory;
import org.apache.http.conn.ssl.SSLConnectionSocketFactory;
import org.apache.http.conn.ssl.SSLContexts;
import org.apache.http.conn.ssl.TrustStrategy;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.impl.conn.PoolingHttpClientConnectionManager;
import org.apache.http.util.EntityUtils;

import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;
import java.io.IOException;
import java.io.InputStream;
import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLEncoder;
import java.security.KeyManagementException;
import java.security.KeyStore;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;
import java.security.cert.CertificateException;
import java.security.cert.X509Certificate;
import java.util.HashMap;
import java.util.Map;

/**
 * @author Administrator
 */
public class HttpsUtils {

    private static PoolingHttpClientConnectionManager poolingHttpClientConnectionManager = null;
    private static Map<String, CloseableHttpClient> httpClients = new HashMap<String, CloseableHttpClient>();
    private static Object o = new Object();

    public static String sendHttpPost(String host, String method, String urlParams, String sendData) throws IOException {

        String requestUrl = host + method + urlParams;

        Map<String, Object> response = request("POST", requestUrl, sendData, null);
        int responseCode = Integer.parseInt(response.get("responseCode").toString());
        byte[] responseBytes = (byte[]) response.get("responseData");
        String responseString;
        responseString = new String(responseBytes);
        //请求返回结果无论成功失败，http-status均为200
        if (responseCode == 200) {
            //返回结果
            return responseString;
        } else {
            throw new IOException(responseCode + ":" + responseString);
        }
    }

    public static byte[] sendHttpGet(String host, String method, String urlParams) throws IOException {
        String requestUrl = host + method + urlParams;
        Map<String, Object> response = request("GET", requestUrl, null, null);
        int responseCode = Integer.parseInt(response.get("responseCode").toString());
        byte[] responseBytes = (byte[]) response.get("responseData");
        //请求返回结果无论成功失败，http-status均为200
        if (responseCode == 200) {
            //返回结果
            return responseBytes;
        } else {
            throw new IOException(responseCode + "");
        }
    }

    public static String urlencode(String data) {
        return urlencode(data, "UTF-8");
    }

    public static String urlencode(String data, String charset) {
        try {
            return URLEncoder.encode(data, charset);
        } catch (UnsupportedEncodingException e) {
            throw new RuntimeException(e.getMessage(), e);
        }
    }

    public static Map<String, Object> request(String method, String url, Object sendData, Map<String, String> headers) throws IOException {


        String requestPath;
        try {
            requestPath = new URL(url).getPath();
        } catch (MalformedURLException e) {
            throw new RuntimeException(e.getMessage(), e);
        }
//        CloseableHttpClient httpClient = getHttpClient(requestPath);
        CloseableHttpClient httpClient = getHttpsClient();
        Map<String, Object> response = null;
        if ("POST".equals(method)) {
            response = sendPost(httpClient, url, headers, sendData);
        } else {
            response = sendGet(httpClient, url, headers);
        }
        return response;
    }

    private static Map<String, Object> sendPost(CloseableHttpClient httpClient, String url, Map<String, String> headers, Object sendData) throws IOException {
        String tag = "[HttpRequester] [POST " + url + "]";
        int responseCode = -1;
        byte[] responseBytes = null;

        HttpPost request = new HttpPost(url);
        if (headers != null && headers.size() > 0) {
            for (String name : headers.keySet()) {
                String value = headers.get(name);
                request.setHeader(name, value);
            }
        }
        if (sendData != null) {
            StringEntity stringEntity = new StringEntity((String) sendData, "UTF-8");
            stringEntity.setContentType("application/json");
            request.setEntity(stringEntity);

            HttpEntity httpEntity = null;
            IOException exception = null;
            for (int i = 0; i < 3; i++) {
                try {
                    CloseableHttpResponse response = httpClient.execute(request);
                    responseCode = response.getStatusLine().getStatusCode();
                    httpEntity = response.getEntity();

                    String responseBody = EntityUtils.toString(httpEntity, "utf-8");
                    if (responseBody != null) {
                        responseBytes = responseBody.getBytes();
                    } else {
                        InputStream respStream = null;
                        try {
                            respStream = httpEntity.getContent();
                            int respBodySize = respStream.available();
                            if (respBodySize <= 0)
                                throw new IOException("Invalid respBodySize: " + respBodySize);
                            responseBytes = new byte[respBodySize];
                            if (respStream.read(responseBytes) != respBodySize)
                                throw new IOException("Read respBody Error");
                        } catch (Exception e) {
                        } finally {
                            if (respStream != null) {
                                respStream.close();
                            }
                        }
                    }

                    exception = null;
                    break;
                } catch (UnsupportedOperationException e) {
                    try {
                        EntityUtils.consume(httpEntity);
                    } catch (IOException e2) {

                    }
                    throw new RuntimeException(e.getMessage(), e);
                } catch (IOException e) {
                    e.printStackTrace();
                    exception = e;
                    try {
                        EntityUtils.consume(httpEntity);
                    } catch (IOException e2) {

                    }
                    if (i < 2) {
                        try {
                            Thread.sleep(5);
                        } catch (InterruptedException e2) {

                        }
                    }
                }
            }
            if (exception != null) {
                throw exception;
            }

        }
        Map<String, Object> response = new HashMap<String, Object>();
        response.put("responseCode", responseCode);
        response.put("responseData", responseBytes);
        String loggerResponseString = getLoggerString(responseBytes, 256);
        return response;
    }

    private static Map<String, Object> sendGet(CloseableHttpClient httpClient, String url, Map<String, String> headers) throws IOException {
        String tag = "[HttpRequester] [GET " + url + "]";
        int responseCode = -1;
        byte[] responseBytes = null;

        HttpGet request = new HttpGet(url);
        if (headers != null && headers.size() > 0) {
            for (String name : headers.keySet()) {
                String value = headers.get(name);
                request.setHeader(name, value);
            }
        }
        HttpEntity httpEntity = null;
        IOException exception = null;
        for (int i = 0; i < 3; i++) {
            try {
                CloseableHttpResponse response = httpClient.execute(request);
                responseCode = response.getStatusLine().getStatusCode();
                httpEntity = response.getEntity();

                byte[] responseBody = EntityUtils.toByteArray(httpEntity);
                if (responseBody != null) {
                    responseBytes = responseBody;
                } else {
                    InputStream respStream = null;
                    try {
                        respStream = httpEntity.getContent();
                        int respBodySize = respStream.available();
                        if (respBodySize <= 0)
                            throw new IOException("Invalid respBodySize: " + respBodySize);
                        responseBytes = new byte[respBodySize];
                        if (respStream.read(responseBytes) != respBodySize)
                            throw new IOException("Read respBody Error");
                    } catch (Exception e) {
                    } finally {
                        if (respStream != null) {
                            respStream.close();
                        }
                    }
                }

                exception = null;
                break;
            } catch (UnsupportedOperationException e) {
                try {
                    EntityUtils.consume(httpEntity);
                } catch (IOException e2) {

                }
                throw new RuntimeException(e.getMessage(), e);
            } catch (IOException e) {
                e.printStackTrace();
                exception = e;
                try {
                    EntityUtils.consume(httpEntity);
                } catch (IOException e2) {

                }
                if (i < 2) {
                    try {
                        Thread.sleep(5);
                    } catch (InterruptedException e2) {

                    }
                }
            }
        }
        if (exception != null) {
            throw exception;
        }
        Map<String, Object> response = new HashMap<String, Object>();
        response.put("responseCode", responseCode);
        response.put("responseData", responseBytes);
        String loggerResponseString = getLoggerString(responseBytes, 256);
        System.out.println(tag + " response " + responseCode + " " + loggerResponseString);
        return response;
    }

    private static String getLoggerString(final byte[] data, int maxLength) {
        String loggerString;
        if (data.length > maxLength) {
            byte[] shortData = new byte[maxLength];
            System.arraycopy(data, 0, shortData, 0, shortData.length);
            try {
                loggerString = new String(shortData, "UTF-8") + "...";
            } catch (UnsupportedEncodingException e) {
                loggerString = new String(shortData) + "...";
            }
        } else {
            try {
                loggerString = new String(data, "UTF-8");
            } catch (UnsupportedEncodingException e) {
                loggerString = new String(data);
            }
        }

        char[] chars = new char[loggerString.length()];
        loggerString.getChars(0, loggerString.length(), chars, 0);
        for (int i = 0; i < chars.length; i++) {
            char c = chars[i];
            if (c == '\n' || c == '\r') {
                chars[i] = ' ';
            }
        }
        return new String(chars);
    }

    private static CloseableHttpClient getHttpClient(String requestPath) {
        if (httpClients.containsKey(requestPath)) {
            return httpClients.get(requestPath);
        }
        if (poolingHttpClientConnectionManager == null) {
            synchronized (o) {
                if (poolingHttpClientConnectionManager == null) {
                    poolingHttpClientConnectionManager = HttpClientUtils.createHttpClientConnectionManager();
                }
            }
        }
        synchronized (httpClients) {
            if (httpClients.containsKey(requestPath)) {
                return httpClients.get(requestPath);
            }
            CloseableHttpClient httpClient = HttpClientUtils.createHttpClient(poolingHttpClientConnectionManager);
            httpClients.put(requestPath, httpClient);
            return httpClient;
        }
    }

    private static class HttpClientUtils {

        // 默认连接超时
        private static int defaultConnectTimeout = 6000;
        // 默认读取超时
        private static int defaultReadTimeout = 30000;

        // 默认连接超时
        private static int longConnectTimeout = 60000;
        // 默认读取超时
        private static int longReadTimeout = 300000;

        public static CloseableHttpClient createHttpClient(PoolingHttpClientConnectionManager connManager) {
            //HttpHost httpHost = new HttpHost("10.211.55.4", 8888);
            CloseableHttpClient httpClient = HttpClients.custom()
                    //.setProxy(httpHost)
                    .setConnectionManager(connManager)
                    .disableContentCompression().setSslcontext(getSslcontext())
//                    .setSSLContext(getSslcontext())
                    .setDefaultRequestConfig(getRequestConfig())
                    .build();
            return httpClient;
        }

        public static PoolingHttpClientConnectionManager createHttpClientConnectionManager() {
            SSLConnectionSocketFactory sslConnectionSocketFactory = null;
            try {
                sslConnectionSocketFactory = new SSLConnectionSocketFactory(getSslcontext(), SSLConnectionSocketFactory.ALLOW_ALL_HOSTNAME_VERIFIER);
            } catch (Exception e) {
                throw new RuntimeException(e.getMessage(), e);
            }
            Registry<ConnectionSocketFactory> socketFactoryRegistry = RegistryBuilder.<ConnectionSocketFactory>create()
                    .register("https", sslConnectionSocketFactory)
                    .register("http", new PlainConnectionSocketFactory())
                    .build();
            PoolingHttpClientConnectionManager cm = new PoolingHttpClientConnectionManager(socketFactoryRegistry);
            cm.setMaxTotal(500);
            cm.setDefaultMaxPerRoute(500);
            return cm;
        }

        private static RequestConfig getRequestConfig() {
            RequestConfig defaultRequestConfig = RequestConfig.custom()
                    .setConnectionRequestTimeout(longConnectTimeout)
                    .setSocketTimeout(longReadTimeout)
                    .build();
            return defaultRequestConfig;
        }

        private static SSLContext getSslcontext() {
            SSLContext sslContext = null;
            try {
                sslContext = SSLContext.getInstance("TLS");
                TrustManager tm = new X509TrustManager() {
                    public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {
                    }

                    public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {
                    }

                    public X509Certificate[] getAcceptedIssuers() {
                        return null;
                    }
                };
                sslContext.init(null, new TrustManager[]{tm}, null);

            } catch (Exception e) {
                e.printStackTrace();
            }
            return sslContext;
        }
    }

    /**
     * 获取https连接（不验证证书）
     *
     * @return
     */
    private static CloseableHttpClient getHttpsClient() {
        RegistryBuilder<ConnectionSocketFactory> registryBuilder = RegistryBuilder.<ConnectionSocketFactory>create();
        ConnectionSocketFactory plainSF = new PlainConnectionSocketFactory();
        registryBuilder.register("http", plainSF);
        // 指定信任密钥存储对象和连接套接字工厂
        try {
            KeyStore trustStore = KeyStore.getInstance(KeyStore.getDefaultType());
            // 信任任何链接
            TrustStrategy anyTrustStrategy = new TrustStrategy() {

                @Override
                public boolean isTrusted(java.security.cert.X509Certificate[] arg0, String arg1) throws java.security.cert.CertificateException {
                    // TODO Auto-generated method stub
                    return true;
                }
            };
            SSLContext sslContext = SSLContexts.custom().useTLS().loadTrustMaterial(trustStore, anyTrustStrategy).build();
            LayeredConnectionSocketFactory sslSF = new SSLConnectionSocketFactory(sslContext, SSLConnectionSocketFactory.ALLOW_ALL_HOSTNAME_VERIFIER);
            registryBuilder.register("https", sslSF);
        } catch (KeyStoreException e) {
            throw new RuntimeException(e);
        } catch (KeyManagementException e) {
            throw new RuntimeException(e);
        } catch (NoSuchAlgorithmException e) {
            throw new RuntimeException(e);
        }
        Registry<ConnectionSocketFactory> registry = registryBuilder.build();
        // 设置连接管理器
        PoolingHttpClientConnectionManager connManager = new PoolingHttpClientConnectionManager(registry);
        // 构建客户端
        return HttpClientBuilder.create().setConnectionManager(connManager).build();
    }
}

```