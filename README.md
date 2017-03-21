# Android_Js_WebView
抛弃使用高风险的WebView addJavascriptInterface方法，通过对js层调用函数及回调函数的包装，支持异步回调，方法参数支持js所有已知的类型，包括number、string、boolean、object、function。

用法

如何开始
初始化Webview WebSettings时允许js脚本执行，同时使用你的注入名和注入类来实例化一个InjectedChromeClient对象，然后关联到你的Webview实例。如demo中的例子（页面中引用的对象名为HostApp，指定的注入类为WebViewJsScope）：

WebView wv = new WebView(this);

WebSettings ws = wv.getSettings();

ws.setJavaScriptEnabled(true);

wv.setWebChromeClient(

    new InjectedChromeClient("HostApp", WebViewJsScope.class)

);

wv.loadUrl("file:///android_asset/test.html");

自定义WebChromeClient子类

如果你需要实现自己的WebChromeClient子类，然后设置到WebView。为了保持InjectedChromeClient的功能，你需要将此类继承自InjectedChromeClient，同时像下面这样覆盖这三个方法。


public class CustomChromeClient extends InjectedChromeClient {

    public CustomChromeClient (String injectedName, Class injectedCls) {
        super(injectedName, injectedCls);
    }

    @Override
    public boolean onJsAlert(WebView view, String url, String message, final JsResult result) {
        // to do your work
        // ...
        return super.onJsAlert(view, url, message, result);
    }

    @Override
    public void onProgressChanged (WebView view, int newProgress) {
        super.onProgressChanged(view, newProgress);
        // to do your work
        // ...
    }

    @Override
    public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result) {
        // to do your work
        // ...
        return super.onJsPrompt(view, url, message, defaultValue, result);
    }
}


防混淆,发布时需在你的混淆配置加入像下面这样的代码，注意返回到页面的自定义Java类以及注入类都要换成你项目中实际使用类名:

#--------------- BEGIN: Gson防混淆 ----------
-keepattributes *Annotation*
-keep class sun.misc.Unsafe { *; }
-keep class com.idea.fifaalarmclock.entity.***
-keep class com.google.gson.stream.** { *; }
#--------------- END ----------

#--------------- BEGIN: 返回到页面的自定义Java对象防混淆 ----------
-keepclassmembers class cn.pedant.SafeWebViewBridge.sample.HostJsScope$RetJavaObj{ *; }
#--------------- END ----------

#--------------- BEGIN: 注入到页面的接口类防混淆 ----------
-keepclassmembers class cn.pedant.SafeWebViewBridge.sample.HostJsScope{ *; }
#--------------- END ----------
