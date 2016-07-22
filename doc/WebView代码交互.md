关于WebView中Java代码和JS代码的交互实现, Android给了一套原生的方案, 我们先来看看原生的用法。后面我们还会讲到其他的开源方法。

JavaScript代码和Android代码是通过addJavascriptInterface()来建立连接的, 我们来看下具体的用法。

1 设置WebView支持JavaScript

```java
webView.getSettings().setJavaScriptEnabled(true);
```

2 在Android工程里定义一个接口

```java
public class WebAppInterface {
    Context mContext;

    /** Instantiate the interface and set the context */
    WebAppInterface(Context c) {
        mContext = c;
    }

    /** Show a toast from the web page */
    @JavascriptInterface
    public void showToast(String toast) {
        Toast.makeText(mContext, toast, Toast.LENGTH_SHORT).show();
    }
}
```

**注意**: API >= 17时, 必须在被JavaScript调用的Android方法前添加@JavascriptInterface注解, 否则将无法识别。

3 在Android代码中将该接口添加到WebView

```java
WebView webView = (WebView) findViewById(R.id.webview);
webView.addJavascriptInterface(new WebAppInterface(this), "Android");
```

这个"Android"就是我们为这个接口取的别名, 在JavaScript就可以通过Android.showToast(toast)这种方式来调用此方法。

4 在JavaScript中调用Android方法

```js
<input type="button" value="Say hello" onClick="showAndroidToast('Hello Android!')" />

<script type="text/javascript">
    function showAndroidToast(toast) {
        Android.showToast(toast);
    }
</script>
```

在JavaScript中我们不用再去实例化WebAppInterface接口, WebView会自动帮我们完成这一工作, 使它能够为WebPage所用。

**注意**:
由于addJavascriptInterface()给予了JS代码控制应用的能力, 这是一项非常有用的特性, 但同时也带来了安全上的隐患, 解决方案是用开源方案
[pedant/safe-java-js-webview-bridge](https://github.com/pedant/safe-java-js-webview-bridge)来实现代码交互。