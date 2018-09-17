注： Volley 在发送 StringRequest 时，getParams\(\) 方法获取的参数是以字符串形式传递的；发送 JSONObjectRequest 时，传参必须通过构造器来传，重写getParams\(\)是无法传递的。

### 3.1.1 服务器需要 JSON 格式的参数，返回 String 格式的数据 {#311-服务器需要-json-格式的参数，返回-string-格式的数据}

```
    StringRequest sr = new StringRequest(Request.Method.POST, Constants.CLOUD_URL, new Response.Listener
<
String
>
() {
        @Override
        public void onResponse(String response) {
            mView.showMessage(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            mView.showMessage(error.getMessage());
        }
    }) {
        @Override
        public byte[] getBody() throws AuthFailureError {
            HashMap
<
String, String
>
 params2 = new HashMap
<
String, String
>
();
            params2.put("name", "Val");
            params2.put("subject", "Test Subject");
            return new JSONObject(params2).toString().getBytes();
        }

        @Override
        public String getBodyContentType() {
            return "application/json";
        }
    };

```

另一种写法

```
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
        headers.put("Content-Type", "application/json; charset=utf-8");
        return headers;
    }

```

### 3.1.2 自定义 {#312-自定义}

```
import java.io.UnsupportedEncodingException;
import java.util.Map;    
import org.json.JSONException;
import org.json.JSONObject;    
import com.android.volley.NetworkResponse;
import com.android.volley.ParseError;
import com.android.volley.Request;
import com.android.volley.Response;
import com.android.volley.Response.ErrorListener;
import com.android.volley.Response.Listener;
import com.android.volley.toolbox.HttpHeaderParser;

public class CustomRequest extends Request
<
JSONObject
>
 {

    private Listener
<
JSONObject
>
 listener;
    private Map
<
String, String
>
 params;

    public CustomRequest(String url, Map
<
String, String
>
 params,
            Listener
<
JSONObject
>
 reponseListener, ErrorListener errorListener) {
        super(Method.GET, url, errorListener);
        this.listener = reponseListener;
        this.params = params;
    }

    public CustomRequest(int method, String url, Map
<
String, String
>
 params,
            Listener
<
JSONObject
>
 reponseListener, ErrorListener errorListener) {
        super(method, url, errorListener);
        this.listener = reponseListener;
        this.params = params;
    }

    protected Map
<
String, String
>
 getParams()
            throws com.android.volley.AuthFailureError {
        return params;
    };

    @Override
    protected Response
<
JSONObject
>
 parseNetworkResponse(NetworkResponse response) {
        try {
            String jsonString = new String(response.data,
                    HttpHeaderParser.parseCharset(response.headers));
            return Response.success(new JSONObject(jsonString),
                    HttpHeaderParser.parseCacheHeaders(response));
        } catch (UnsupportedEncodingException e) {
            return Response.error(new ParseError(e));
        } catch (JSONException je) {
            return Response.error(new ParseError(je));
        }
    }

    @Override
    protected void deliverResponse(JSONObject response) {
        // TODO Auto-generated method stub
        listener.onResponse(response);
    }
}

```

```
    RequestQueue requestQueue = Volley.newRequestQueue(getActivity());
    CustomRequest jsObjRequest = new CustomRequest(Method.POST, url, params, this.createRequestSuccessListener(), this.createRequestErrorListener());

    requestQueue.add(jsObjRequest);
```



