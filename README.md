# webview
Exemplo de implementação de webview no android
Readme
Sobre o webview (http://developer.android.com/intl/pt-br/guide/webapps/webview.html)

A webview seria algo como um browser, só que dentro do próprio aplicativo. Isto seria interessante, pois o usuário não
precisa abandonar o app para olhar um link que ele clicou em nosso aplicativo. Podemos habilitar o javascript na webview, caso
o site do link use, bem como utilizar este javascript do site dentro de nosso código do aplicativo.

Crie uma activity em branco no android studio chamada TestaWebView, no arquivo de layout (que tem o nome testa_web_view.xml,
dentro de res/layout referente à activity), temos algo assim:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView xmlns:android="http://schemas.android.com/apk/res/android"
   android:id="@+id/webview"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"

   />
```
Por enquanto vamos deixar de lado esta activity, e iremos criar outra, onde digitamos a url e então vai fazer uma instância desta activity TestaWebView passando a url que foi digitada. Para isso, vá em File/New/Activity/Blank Activity, o nome pode ser MainActivity. No arquivo activity_main.xml, teremos a seguinte estrutura:
```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
   xmlns:app="http://schemas.android.com/apk/res-auto"
   xmlns:tools="http://schemas.android.com/tools"
   android:layout_width="match_parent"
   android:layout_height="match_parent"
   android:fitsSystemWindows="true"
   tools:context="com.example.ederpaulo.webview.MainActivity">

   <EditText
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:id="@+id/url"
       android:hint="@string/dicaurl"
       android:width="350dp"
       android:inputType="textUri"
       />

   <Button
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:id="@+id/botaoWV"
       android:onClick="abrirSite"
       android:text="@string/abrir"
       android:layout_marginTop="40dp"

       />

</android.support.design.widget.CoordinatorLayout>
```
Não esqueça de criar as strings respectivas, o android studio vai te lembrar disso.
No evento onClick do botão, temos o valor “abrirSite”, que é um método dentro do código java. Vamos criar este método.
Lembrando que este método tem que ser fora do evento onCreate da activity.
Inicialmente, fora do método abrirSite, criamos uma variável global na classe, para escrevermos um conteúdo no textView, então:
```java
TextView textView;
```

Após, no método onCreate, abaixo do setContentView, faremos assim:
```java
textView = (TextView) findViewById(R.id.url);
textView.setText("http://www.");
```
Pegamos o textview do xml, cujo id é url, e setamos um prefixo para auxiliar na digitação da url, no caso, http://www.

Agora, no método abrirSite(View view), temos o seguinte código:
```java
public void abrirSite(View view) {

textView = (TextView) findViewById(R.id.url);
String url = textView.getText().toString().trim();

if (!(url.length() == 0)){

   Intent intent = new Intent(this, testaWebView.class);
   intent.putExtra("URL", url);
   startActivity(intent);
} else {
   Toast.makeText(this, "URL inválida", Toast.LENGTH_SHORT).show();
   }

}
```
Novamente, pegamos o textview do xml cujo id é url, então atribuímos o conteúdo à uma string. Repare o uso da funçao trim(), para retirar espaços em branco, pois, no if realizamos a checagem se o conteúdo está vazio. Se não estiver vazio, então criamos uma intent, já referenciando a classe testaWebView.class, que foi criada lá no começo. Utilizamos o método putExtra, pra passar a url que digitamos no textview, e posteriormente, ‘startamos’ a activity. Caso o tamanho da url seja 0, então uma mensagem Toast será mostrada avisando o usuário.

Agora na classe testaWebView, temos o seguinte cógido no método onCreate:
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   setContentView(R.layout.testa_web_view);

   Bundle bundle = getIntent().getExtras();
   String url = null;
   if (bundle != null){
       url = bundle.getString("URL");
       Toast.makeText(this, "Abrindo " + url, Toast.LENGTH_SHORT).show();

       WebView webView = (WebView) findViewById(R.id.webview);

       WebSettings ws = webView.getSettings();
       ws.setJavaScriptEnabled(true);
       webView.loadUrl(url);
   }
}
```
Até o setContentView não temos nada de novo.
Abaixo, criamos um Bundle, que obtém os ‘extras’ caso houver algum, e neste caso tem que haver mesmo. Novamente, criamos uma string pra url, e verificamos, se o bundle não for nulo, então pegamos o conteúdo referente à chave ‘URL’, criamos um toast pra avisar o usuário que a url está abrindo, e então começa a mágica: criamos uma instância de um objeto do tipo webview, referenciamos ele ao webview do xml, cujo id também é webview. Após, habilitamos o javascript do webview, ‘ws.setJavaScriptEnabled(true)’, e então carregamos a url que pegamos no bundle.

No entanto, em alguns testes que fiz, algumas url’s não abrem no webview, e sim no navegador padrão do dispositivo, o que não é desejável, pois perde a performance neste caso. Para solucionar isso, devemos adicionar a seguinte linha no código, antes de dar um loadUrl:
```java
webView.setWebViewClient(new WebViewClient());
```
Feito isso, então todas as páginas abrirão obrigatóriamente no webview, não mais no browser padrão do dispositivo.
