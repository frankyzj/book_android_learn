# 3.Volley的使用 {#3volley的使用}

## 3.1 在 Application 中封装 RequestQueue 和 ImageLoader 等的方法。 {#31-在-application-中封装-requestqueue-和-imageloader-等的方法。}

```
package com.prient.focuslibrary.security;

import android.app.Application;
import android.content.res.Configuration;
import android.text.TextUtils;

import com.android.volley.Request;
import com.android.volley.RequestQueue;
import com.android.volley.toolbox.HurlStack;
import com.android.volley.toolbox.ImageLoader;
import com.android.volley.toolbox.Volley;
import com.prient.focuslibrary.Interface.Callback;
import com.prient.focuslibrary.net.HttpsRelative;
import com.prient.focuslibrary.net.LruBitmapCache;

import java.io.IOException;
import java.io.InputStream;
import java.security.cert.CertificateException;
import java.security.cert.CertificateFactory;

import javax.net.ssl.SSLSocketFactory;

public class AppController extends Application implements Callback
<
String
>
{

    private String certificateName;
    private RequestQueue mRequestQueue;
    private RequestQueue mHttpsRequestQueue;
    private ImageLoader mImageLoader;

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
    }

    public HurlStack setStack(String certificateName) {

        InputStream cerInputStream = null;
        try {
            //InputStream cerInputStream = getResources().openRawResource();
            cerInputStream = getAssets().open(certificateName);
            SSLSocketFactory sslSocketFactory = HttpsRelative.initSSLSocketFactory(cerInputStream);
            //HurlStack两个参数默认都是null，如果传入SSLSocketFactory,则会以Https的方式来请求网络
            HurlStack hurlStack = new HurlStack(null, sslSocketFactory);

            return hurlStack;

        } catch (IOException exception) {
            exception.printStackTrace();
        } finally {
            return null;
        }
    }

    @Override
    public void excute(String result) {
        certificateName = result;
    }

    public RequestQueue getRequestQueue() {
        if(mRequestQueue == null){
            mRequestQueue = Volley.newRequestQueue(getApplicationContext());
        }
        return mRequestQueue;
    }

    public ImageLoader getImageLoader(){
        getRequestQueue();
        if(mImageLoader == null){
            mImageLoader = new ImageLoader(this.mRequestQueue, new LruBitmapCache());
        }
        return this.mImageLoader;
    }

    public 
<
T
>
 void addToRequestQueue(Request
<
T
>
 req, String tag){
        //如果tag为空，设置默认值
        req.setTag(TextUtils.isEmpty(tag) ? TAG :tag);
        getRequestQueue().add(req);
    }

    public 
<
T
>
 void addToRequestQueue(Request
<
T
>
 req){
        req.setTag(TAG);
        getRequestQueue().add(req);
    }

    public void cancelPendinRequests(Object tag) {
        if(mRequestQueue != null) {
            mRequestQueue.cancelAll(tag);
        }
    }

    public RequestQueue getHttpsRequestQueue() {
        if (mHttpsRequestQueue == null) {
            mHttpsRequestQueue = Volley.newRequestQueue(getApplicationContext(), setStack(certificateName));
        }
        return mHttpsRequestQueue;
    }

    public ImageLoader getHttpsImageLoader(){
        getHttpsRequestQueue();
        if(mImageLoader == null){
            mImageLoader = new ImageLoader(this.getHttpsRequestQueue(), new LruBitmapCache());
        }
        return this.mImageLoader;
    }

    public 
<
T
>
 void addToHttpsRequestQueue(Request
<
T
>
 req, String tag) {
        req.setTag(TextUtils.isEmpty(tag) ? TAG : tag);
        getHttpsRequestQueue().add(req);
    }
    public 
<
T
>
 void addToHttpsRequestQueue(Request
<
T
>
 req) {
        req.setTag(TAG);
        getHttpsRequestQueue().add(req);
    }

    public void cancelPendingRequests(Object tag) {
        if(mHttpsRequestQueue != null){
            mHttpsRequestQueue.cancelAll(tag);
        }
    }
}

```

## 3.2 使用 Volley 向服务器请求不同类型的数据 {#32-使用-volley-向服务器请求不同类型的数据}

```
package com.prient.demovolley;

import android.app.ProgressDialog;
import android.content.res.Resources;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.AsyncTask;
import android.os.Bundle;
import android.support.design.widget.Snackbar;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.widget.ImageView;
import android.widget.TextView;

import com.android.volley.AuthFailureError;
import com.android.volley.Cache;
import com.android.volley.Request;
import com.android.volley.RequestQueue;
import com.android.volley.Response;
import com.android.volley.VolleyError;
import com.android.volley.VolleyLog;
import com.android.volley.toolbox.ImageLoader;
import com.android.volley.toolbox.ImageRequest;
import com.android.volley.toolbox.JsonArrayRequest;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.NetworkImageView;
import com.android.volley.toolbox.StringRequest;
import com.android.volley.toolbox.Volley;
import com.prient.demovolley.app.AppController;
import com.prient.demovolley.utils.LruBitmapCache;

import org.json.JSONArray;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.UnsupportedEncodingException;
import java.net.URL;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.cert.CertificateFactory;
import java.security.cert.X509Certificate;
import java.util.HashMap;
import java.util.Map;

import javax.net.ssl.HostnameVerifier;
import javax.net.ssl.HttpsURLConnection;
import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLSession;
import javax.net.ssl.SSLSocketFactory;
import javax.net.ssl.TrustManager;
import javax.net.ssl.TrustManagerFactory;

public class MainActivity extends AppCompatActivity {

    private String TAG = getClass().getSimpleName();
    private TextView tv1,tv2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

//        tv1 = (TextView) findViewById(R.id.tv1);
//        getJsonObjectRequest();
//        tv2 = (TextView) findViewById(R.id.tv2);
//        geteJsonArrayRequest();
//        getStringRequest();
//        postJsonObjectRequest();
//        postJsonObjectRequestWithHeader();
//
//        makeImageRequest("");
//        //BitmapFactory
//
//        loadingRequestFromCache("");
//        invalidateCache("");
//        deleteCacheForPaticulatUrl("");
//        clearAllCache();
//
//        cancelOneRequest("tag");
//        cancelAllRequest();
//        setRequestPrioritization("");
//
//        getBitmapSizeAndType();
//
//        ImageView mImageView = (ImageView) findViewById(R.id.img_normal);
//        mImageView.setImageBitmap(
//                decodeSampledBitmapFromResource(getResources(), R.id.img_normal, 100, 100));
//
//        new DownloadThread().start();

    }

    private void getBitmapSizeAndType() {
        BitmapFactory.Options options = new BitmapFactory.Options();
        options.inJustDecodeBounds = true;
        BitmapFactory.decodeResource(getResources(), R.id.img_normal, options);
        int imageHeight = options.outHeight;
        int imageWidth = options.outWidth;
        String imageType = options.outMimeType;
    }

    /**
     * 根据传入的值计算压缩比率 inSampleSize
     * @param options
     * @param reqWidth
     * @param reqHeight
     * @return
     */
    public static int calculateInSampleSize(BitmapFactory.Options options,
                                            int reqWidth, int reqHeight) {
        // 源图片的高度和宽度
        final int height = options.outHeight;
        final int width = options.outWidth;
        int inSampleSize = 1;
        if (height 
>
 reqHeight || width 
>
 reqWidth) {
            // 计算出实际宽高和目标宽高的比率
            final int heightRatio = Math.round((float) height / (float) reqHeight);
            final int widthRatio = Math.round((float) width / (float) reqWidth);
            // 选择宽和高中最小的比率作为inSampleSize的值，这样可以保证最终图片的宽和高
            // 一定都会大于等于目标的宽和高。
            inSampleSize = heightRatio 
<
 widthRatio ? heightRatio : widthRatio;
        }
        return inSampleSize;
    }

    public static Bitmap decodeSampledBitmapFromResource(Resources res, int resId,
                                                         int reqWidth, int reqHeight) {
        // 第一次解析将inJustDecodeBounds设置为true，来获取图片大小
        final BitmapFactory.Options options = new BitmapFactory.Options();
        options.inJustDecodeBounds = true;
        BitmapFactory.decodeResource(res, resId, options);
        // 调用上面定义的方法计算inSampleSize值
        options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight);
        // 使用获取到的inSampleSize值再次解析图片
        options.inJustDecodeBounds = false;
        return BitmapFactory.decodeResource(res, resId, options);
    }

    public void loadBitmap(int resId, ImageView imageView) {
        final String imageKey = String.valueOf(resId);
        final Bitmap bitmap = new LruBitmapCache().getBitmap(imageKey);
        if (bitmap != null) {
            imageView.setImageBitmap(bitmap);
        } else {
            imageView.setImageResource(R.drawable.ic_launcher);
            BitmapWorkerTask task = new BitmapWorkerTask(imageView);
            task.execute(resId);
        }
    }

    class BitmapWorkerTask extends AsyncTask
<
Integer, Void, Bitmap
>
 {

        BitmapWorkerTask(ImageView imageView){

        }
        // 在后台加载图片。
        @Override
        protected Bitmap doInBackground(Integer... params) {
            final Bitmap bitmap = decodeSampledBitmapFromResource(
                    getResources(), params[0], 100, 100);
            new LruBitmapCache().putBitmap(String.valueOf(params[0]), bitmap);
            return bitmap;
        }
    }

    /**
     * 同时有多个请求，可以设置各请求的优先级，Normal、Low、Immediate、High.
     */
    private void setRequestPrioritization(String url) {
        final Request.Priority priority = Request.Priority.IMMEDIATE;
        StringRequest stringRequest = new StringRequest(Request.Method.GET,
                url, new Response.Listener
<
String
>
() {
            @Override
            public void onResponse(String response) {
                Log.d(TAG, response.toString());
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                VolleyLog.d(TAG, "Error: " + error.getMessage());
            }
        }) {
            @Override
            public Priority getPriority() {
                return priority;
            }
        };
    }

    private void cancelAllRequest() {
        AppController.getApplication().getRequestQueue().cancelAll(null);
    }

    private void cancelOneRequest(String tag) {
        AppController.getApplication().getRequestQueue().cancelAll(tag);
    }

    private void clearAllCache() {
        AppController.getApplication().getRequestQueue().getCache().clear();
    }

    private void deleteCacheForPaticulatUrl(String url) {
        AppController.getApplication().getRequestQueue().getCache().remove(url);
    }

    /**
     * 调用该方法后，在从服务器下载到新数据前，会使用缓存数据
     */
    private void invalidateCache(String key) {
        AppController.getApplication().getRequestQueue().getCache().invalidate(key,true);
    }

    private void loadingRequestFromCache(String key) {
        Cache cache = AppController.getApplication().getRequestQueue().getCache();
        Cache.Entry entry = cache.get(key);
        if(entry != null){
            try {
                String data = new String(entry.data, "UTF-8");
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        } else {
            //没有缓存，请求网络
        }
    }

    private void makeImageRequest(String imgUrl) {
        //方法1：
        // NetworkImageView并不需要提供任何设置最大宽高的方法也能够对加载的图片进行压缩。
        // 这是由于NetworkImageView是一个控件，在加载图片的时候它会自动获取自身的宽高，
        // 然后对比网络图片的宽度，再决定是否需要对图片进行压缩。
        NetworkImageView imgNetWorkView = (NetworkImageView) findViewById(R.id.img_net_work_view);
        imgNetWorkView.setDefaultImageResId(R.drawable.ic_launcher);
        imgNetWorkView.setErrorImageResId(R.drawable.ic_launcher);

        ImageLoader imageLoader = AppController.getApplication().getImageLoader();
        imgNetWorkView.setImageUrl(imgUrl, imageLoader);

        //方法2:
        final ImageView img = (ImageView) findViewById(R.id.img_normal);
        ImageLoader imageLoader1 = AppController.getApplication().getImageLoader();
        imageLoader.get(imgUrl, new ImageLoader.ImageListener() {
            @Override
            public void onResponse(ImageLoader.ImageContainer response, boolean isImmediate) {
                if(response.getBitmap() != null) {
                    img.setImageBitmap(response.getBitmap());
                }
            }

            @Override
            public void onErrorResponse(VolleyError error) {
                Log.d(TAG, "Error: " + error.getMessage());
            }
        });

        // 方法3：
        // 第三第四个参数分别用于指定允许图片最大的宽度和高度，如果指定的网络图片的宽度或高度
        // 大于这里的最大值，则会对图片进行压缩，指定成0的话就表示不管图片有多大，都不会进行
        // 压缩。第五个参数用于指定图片的颜色属性，Bitmap.Config下的几个常量都可以在这里使
        // 用，其中ARGB_8888可以展示最好的颜色属性，每个图片像素占据4个字节的大小，而RGB_565
        // 则表示每个图片像素占据2个字节大小。
        ImageRequest imageRequest = new ImageRequest(imgUrl, new Response.Listener
<
Bitmap
>
() {
            @Override
            public void onResponse(Bitmap response) {
                img.setImageBitmap(response);
            }
        }, 0, 0, Bitmap.Config.RGB_565, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                img.setImageResource(R.drawable.ic_launcher);
            }
        });

        //以下方法提供图片加载中和图片下载失败时显示的图片,第一个参数指定用于显示图片的ImageView控件，
        // 第二个参数指定加载图片的过程中显示的图片，第三个参数指定加载图片失败的情况下显示的图片
        imageLoader1.get(imgUrl, ImageLoader.getImageListener(img,
                R.drawable.ic_launcher, R.drawable.ic_launcher));
        //指定图片允许的最大宽度和高度
        imageLoader.get(imgUrl, ImageLoader.getImageListener(img,
                R.drawable.ic_launcher, R.drawable.ic_launcher), 200, 200);
    }

    private void postJsonObjectRequestWithHeader() {
        String tag_json_obj = "tag_json_obj";
        String url = "http://api.androidhive.info/volley/person_object.json";

        final ProgressDialog progressDialog = new ProgressDialog(this);
        progressDialog.setMessage("Loading...");
        progressDialog.show();

        JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.POST,
                url, null, new Response.Listener
<
JSONObject
>
() {
            @Override
            public void onResponse(JSONObject response) {
                Log.d(TAG, response.toString());
                progressDialog.hide();
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                VolleyLog.d(TAG, "Error: " + error.getMessage());
            }
        }){
            @Override
            public Map
<
String, String
>
 getHeaders() throws AuthFailureError {
                HashMap
<
String, String
>
 headers = new HashMap
<
String, String
>
();
                headers.put("Content-Type", "application/json");
                headers.put("apiKey", "xxxxxxxxxxxxxx");
                return headers;
            }
        };
        //设置不要缓存
        jsonObjectRequest.setShouldCache(false);

        AppController.getApplication().addToRequestQueue(jsonObjectRequest, tag_json_obj);
    }

    private void postJsonObjectRequest() {
        String tag_post_json_obj = "post_json_obj_req";
        String url = "http://api.androidhive.info/volley/person_object.json";

        final ProgressDialog progressDialog = new ProgressDialog(this);
        progressDialog.setMessage("Loading");
        progressDialog.show();

        JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.POST,
                url, null, new Response.Listener
<
JSONObject
>
() {
            @Override
            public void onResponse(JSONObject response) {
                Log.d(TAG, response.toString());
                progressDialog.hide();
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                VolleyLog.d(TAG, "Error: " + error.getMessage());
                progressDialog.hide();
            }
        }){
            @Override
            protected Map
<
String, String
>
 getParams() throws AuthFailureError {

                Map
<
String, String
>
 params = new HashMap
<
>
();
                params.put("name", "Androidhive");
                params.put("email", "Androidhive@123.info");
                params.put("password", "password123");
                return params;
            }
        };
        AppController.getApplication().addToRequestQueue(jsonObjectRequest,tag_post_json_obj);
    }

    private void getStringRequest() {
        String tag_string_req = "string_req";
        String url = "http://api.androidhive.info/volley/string_response.html";

        final ProgressDialog progressDialog = new ProgressDialog(this);
        progressDialog.setMessage("Loading");
        progressDialog.show();

        StringRequest stringRequest = new StringRequest(Request.Method.GET,
                url, new Response.Listener
<
String
>
() {
            @Override
            public void onResponse(String response) {
                Log.d(TAG, response);
                progressDialog.hide();
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                VolleyLog.d(TAG, "Error: " + error.getMessage());
                progressDialog.hide();
            }
        });

        AppController.getApplication().addToRequestQueue(stringRequest,tag_string_req);
    }

    private void geteJsonArrayRequest() {
        String tag_json_ary = "json_array_req";
        String url = "http://api.androidhive.info/volley/person_array.json";
        final ProgressDialog progressDialog = new ProgressDialog(this);
        progressDialog.setMessage("Loading...");
        progressDialog.show();

        JsonArrayRequest jsonArrayRequest = new JsonArrayRequest(url,
                new Response.Listener
<
JSONArray
>
() {

                    @Override
                    public void onResponse(JSONArray response) {
                        Log.d(TAG, response.toString());
                        progressDialog.hide();
                    }
                }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                VolleyLog.d(TAG, "Error: " + error.getMessage());
                progressDialog.hide();
            }
        });
        AppController.getApplication().addToRequestQueue(jsonArrayRequest, tag_json_ary);
    }

    private void getJsonObjectRequest() {
        //用来取消请求的Tag
        String tag_json_obj = "json_obj_req";
        String url = "http://api.androidhive.info/volley/person_object.json";
        final ProgressDialog progressDialog = new ProgressDialog(this);
        progressDialog.setMessage("Loading");
        progressDialog.show();

        JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.GET,
                url, null, new Response.Listener
<
JSONObject
>
() {
            @Override
            public void onResponse(JSONObject response) {
                tv1.setText(response.toString());
                Log.d(TAG, response.toString());
                progressDialog.hide();
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                VolleyLog.d(TAG, "Error: " + error.getMessage());
                Snackbar.make(tv1,error.getMessage(),Snackbar.LENGTH_SHORT).show();
            }
        });
        //把请求添加到请求队列
        AppController.getApplication().addToRequestQueue(jsonObjectRequest, tag_json_obj);
    }

    class DownloadThread extends Thread {
        @Override
        public void run() {

          HttpsURLConnection.setDefaultHostnameVerifier(new HostnameVerifier(){
                public boolean verify(String string,SSLSession ssls) {
                    try {
                        Log.i("ss", "string1:" + string);
                        Log.i("ss", "string2:" + ssls.getPeerHost());
                        Log.i("ss", "getProtocol:" + ssls.getProtocol());
                    }catch (Exception e){
                        e.printStackTrace();
                    }
                    return true;
                }
                }
            );

            HttpsURLConnection conn = null;
            InputStream is = null;
            try {

                URL url = new URL("https://192.168.30.233:7103/test/");
                conn = (HttpsURLConnection) url.openConnection();


                //创建X.509格式的CertificateFactory
                CertificateFactory cf = CertificateFactory.getInstance("X.509");
                //从asserts中获取证书的流
                InputStream cerInputStream = getAssets().open("root-cert.der");//SRCA.cer
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

                // 创建一个默认类型的KeyStore，存储我们信任的证书1
                String keyStoreType = KeyStore.getDefaultType();
                Log.i("ss", "keyStoreType:" + keyStoreType);
                KeyStore keyStore = KeyStore.getInstance(keyStoreType);
                keyStore.load(null, null);
                //将证书ca作为信任的证书放入到keyStore中
                keyStore.setCertificateEntry("ca", ca);

                //TrustManagerFactory是用于生成TrustManager的，我们创建一个默认类型的TrustManagerFactory
                String tmfAlgorithm = TrustManagerFactory.getDefaultAlgorithm();
                Log.i("ss", "tmfAlgorithm:" + tmfAlgorithm);

                Log.i("ss","HostnameVerifier1:"+HttpsURLConnection.getDefaultHostnameVerifier().getClass());


                TrustManagerFactory tmf = TrustManagerFactory.getInstance(tmfAlgorithm);
                //用我们之前的keyStore实例初始化TrustManagerFactory，这样tmf就会信任keyStore中的证书
                tmf.init(keyStore);
                //通过tmf获取TrustManager数组，TrustManager也会信任keyStore中的证书
                TrustManager[] trustManagers = tmf.getTrustManagers();


                //创建TLS类型的SSLContext对象， that uses our TrustManager
                SSLContext sslContext = SSLContext.getInstance("SSLv3");
                //用上面得到的trustManagers初始化SSLContext，这样sslContext就会信任keyStore中的证书
                sslContext.init(null, trustManagers, null);
                Log.i("ss","getProtocol:"+sslContext.getProtocol());
                Log.i("ss","getProvider:"+sslContext.getProvider());

                //通过sslContext获取SSLSocketFactory对象
                SSLSocketFactory sslSocketFactory = sslContext.getSocketFactory();
                //将sslSocketFactory通过setSSLSocketFactory方法作用于HttpsURLConnection对象
                //这样conn对象就会信任我们之前得到的证书对象
                Log.i("ss","HostnameVerifier2:"+conn.getHostnameVerifier().getClass());
                conn.setSSLSocketFactory(sslSocketFactory);

                is = conn.getInputStream();

                //将得到的InputStream转换为字符串
                final String str = getStringByInputStream(is);
                Log.i("ss", "Result_String" + str);
               /* handler.post(new Runnable() {
                    @Override
                    public void run() {
                        textView.setText(str);
                        btn.setEnabled(true);
                    }
                });*/
            } catch (Exception e) {
                e.printStackTrace();
               /* final String errMessage = e.getMessage();
                handler.post(new Runnable() {
                    @Override
                    public void run() {
                        btn.setEnabled(true);
                        Toast.makeText(MainActivity.this, errMessage, Toast.LENGTH_LONG).show();
                    }
                });*/
            } finally {
                if (conn != null) {
                    conn.disconnect();
                }
            }
        }
    }

    public String getStringByInputStream(InputStream is) {
        BufferedReader reader = new BufferedReader(new InputStreamReader(is));
        StringBuilder sb = new StringBuilder();
        String line = null;
        try {
            while ((line = reader.readLine()) != null) {
                sb.append(line + "/n");
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                is.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return sb.toString();
    }
}
```



