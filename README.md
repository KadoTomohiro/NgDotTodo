# NgDotTodo

ASP.NET Core Web API + Angular Project.

## 環境

* .NET Core SDK
* Node
* Angular CLI

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



## Development server
## Code scaffolding
## Build
## Running unit tests
## Running end-to-end tests
