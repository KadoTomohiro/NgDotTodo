# NgDotTodo

ASP.NET Core Web API + Angular Project.

## 環境

* dotnet cli 2.0.0
* @angular/cli: 1.4.9

エディタはvs codeがいいかも。

## プロジェクト作成

同一プロジェクト内にAngularと.NetCoreを共存させる

### .NET Coreプロジェクト作成

* `dotnet`コマンドで作る
* テンプレートはWebAPI

```sh
$  dotnet new webapi -n NgDotTodo 
```

#### 動作確認

.NET 起動

```sh
$ dotnet run
Content root path: /my/project/dir
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

ブラウザから`http://localhost:5000/api/values`にアクセス。または別ターミナルで、

```sh
$ curl http://localhost:5000/api/values
```

結果を確認。

```json
["value1","value2"]
```

URLや結果は`Controllers`ディレクトリ内のControllerクラスに依る。

### Angularプロジェクト作成

* Angular CLIで作る
* `dotnet new`で作ったプロジェクトディレクトリ内に出力(`-dir`オプション)
* Gitはまとめてやるので飛ばす(`-sg`オプション)
  * Gitを飛ばさず、後で.NET Core資産を加えたほうがよかったか？
* 他は好みで

```sh
$ ng new NgDotTodo -dir NgDotTodo -p td --routing true -sg
```

#### 動作確認

```sh
$ ng serve
```

ブラウザで`http://localhost:4200`にアクセスして、Angularが動作していることを確認する。

### プロジェクトの修正

#### Angular srcの変更

Angular資産のディレクトリが`src`だとなんのソースか紛らわしいので、`Crient`に変更。

```
$ mv src/ Client
```

`ng new`の際に`-sd Client`オプションを指定すれば良さそうだが、`app/`ディレクトリ以外は`src`に出力されてしまってうまく行かない。

`.angula-cli.json`を修正

```json
{
  "apps": {
      "root": "Client"
  }
}
```

#### 出力先を修正

.NET Coreは`wwwroot`をクライアントのルートディレクトリとして扱う（変更は可能）。buildの出力先を変更する。

`.angula-cli.json`を修正

```json
{
  "apps": {
        "outDir": "wwwroot"
  } 
}
```

### ASP.NETからAngular Appを参照できるようにする

参考: [ASP.NET Core上でSPAを動かしたい](https://qiita.com/kuluna/items/39ef29b906696a034070)

`Startup.cs`を修正

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
     loggerFactory.AddConsole(Configuration.GetSection("Logging"));
     loggerFactory.AddDebug();

　   app.UseStaticFiles();

　    app.UseMvc(routes =>
     {
         routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
         routes.MapSpaFallbackRoute("spa-fallback", new { controller = "Home", action = "Index" });
     });
}
```

`Controllers/HomeController.cs`を追加

```c#
using Microsoft.AspNetCore.Mvc;

namespace NgDotTodo.Controllers
{
  /// <summary>
  /// Default MVC Controller
  /// </summary>
  public class HomeController : Controller
  {
    /// <summary>
    /// Open the wwwroot/index.html
    /// </summary>
    public IActionResult Index() => File("/index.html", "text/html");
  }
}
```

#### 動作確認

```sh
$ ng build
$ dotnet run
```

`http://localhost:5000`でAngularアプリが動けばOK

#### 課題

* live-reloadができない
