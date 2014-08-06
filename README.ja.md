# Aura.Web_Project

Aura.Web_Projectは、Webプロジェクト向けの最小限のフレームワークです。

Auraチームにおける "最小限" という言葉の意味は、本当に最小だということです。
パッケージに含まれている機能は、DIコンテナ、コンフィギュレーションシステム、ルーター、ディスパッチャー、リクエストオブジェクトとレスポンスオブジェクト、およびロギングのみです。

最小限ということは、"制限が厳しい" ことではありません。
2段階のコンフィギュレーションシステムを持つDIコンテナでは、広範かつプログラマティックにサービス定義が可能です。
ルーターとディスパッチャーは、アプリケーションを反復的にリファクタリングしていくことを念頭に置いて作られています。
したがって、最初はマイクロフレームワークのようにクロージャーを使ってコントローラーを作り、後から開発者が独自に設計したコントローラーオブジェクトへ切り替えることもできます。

## はじめに

### 要件

PHP 5.4 以降の環境が必要です。Auraライブラリパッケージとは異なり、このプロジェクトパッケージには、ユーザーランドにおいて、次のパッケージ依存があります。これらはさらに別のパッケージに依存していることもあります。

- [aura/web-kernel](https://packagist.org/packages/aura/web-kernel)
- [monolog/monolog](https://packagist.org/packages/monolog/monolog)

### インストール方法

Composerを使ってプロジェクトを指定した `{$PROJECT_PATH}` へインストール:

    composer create-project --stability=dev aura/web-project {$PROJECT_PATH}
    
このコマンドを実行すると、プロジェクトスケルトンが作られ、必要なパッケージがすべてインストールされます。

### テスト

[![ビルドステータス](https://travis-ci.org/auraphp/Aura.Web_Project.png)](https://travis-ci.org/auraphp/Aura.Web_Project)

このプロジェクトは、[PHPUnit](http://phpunit.de) によるコードカバレッジが100%です。コマンドラインからテストを実行するには、`tests/project/` ディレクトリへ移動して `./phpunit.sh` を起動してください。

テストを実行する代わりに、プロジェクトのインストールが完了していれば、`web/` ディレクトリをドキュメントルートとしてPHPのビルトインサーバーを起動できます:

    cd {$PROJECT_PATH}
    php -S localhost:8000 -t web/

Webブラウザで <http://localhost:8000> へアクセスすると、"Hello World!" と表示されるはずです。確認できたら、ビルトインサーバーのプロセスを終了させてください。
(PHPのビルトインサーバーはテストでのみ利用し、プロダクションには利用しないようにしてください)

### PSR 準拠

このプロジェクトは、[PSR-1][]、[PSR-2][]、[PSR-4][] に準拠しています。規約に準拠していない箇所を見つけたら、プルリクエストでパッチを送ってください。

[PSR-1]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md
[PSR-2]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md
[PSR-4]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md

### コミュニティ

質問、フィードバック、およびAuraコミュニティへの連絡は、[Googleグループ](http://groups.google.com/group/auraphp)、[Twitterの@auraphp](http://twitter.com/auraphp)、Freenodeの #auraphp を使ってください。

### サービス

このパッケージでは、以下で定義されているサービスを使っています:

- [Aura.Project_Kernel](https://github.com/auraphp/Aura.Project_Kernel#services)
- [Aura.Web_Kernel](https://github.com/auraphp/Aura.Web_Kernel#services)

このプロジェクトでは、次のサービスをリセットします:

- `logger`: `Monolog\Logger` のインスタンス

## 入門

### コンポーネントパッケージ

このプロジェクトは、独立したAuraのパッケージ群を、機能する1つの形にまとめ上げています。個々のパッケージについての詳細は、パッケージのドキュメントを参照してください。

DIコンテナは、Auraプロジェクトにおけるさまざまな操作の中心となっています。あらかじめ[Aura.Diのドキュメント](https://github.com/auraphp/Aura.Di)を読んでおいてください。

また、[Aura.Router](https://github.com/auraphp/Aura.Router)、[Aura.Dispatcher](https://github.com/auraphp/Aura.Dispatcher)、および[Aura.Web](https://github.com/auraphp/Aura.Web)の_Request_オブジェクトと_Response_オブジェクトについても、ドキュメントを読んでおいてください。

### プロジェクトのコンフィギュレーション

すべてのAuraプロジェクトは、同じ方法で設定できます。詳細は[共通のコンフィギュレーションドキュメント](https://github.com/auraphp/Aura.Project_Kernel#configuration)を参照してください。

### ロギング

ログは自動的に `{$PROJECT_PATH}/tmp/log/{$mode}.log` ファイルに出力されます。
特定のモードでロギングの動作を変更したい場合は、対応するコンフィギュレーションファイル(例えば `config/Dev.php`)で `logger` サービスを変更してください。

### ルーティングとディスパッチ

ルーティングとディスパッチは、プロジェクトレベルの `config/` クラスファイルで設定します。
すべてのモードで有効なルートを定義するには、プロジェクトレベルの `config/Common.php` クラスファイルに記述してください。
`dev` のみといった特定のモードでのみ有効なルートを定義するには、モードに対応するコンフィギュレーションファイルに記述してください。

ルーティングとディスパッチの定義の仕方について、3つのスタイルを紹介します。

#### マイクロフレームワークスタイル

Auraは、[Action Domain Responder](https://github.com/pmjones/mvc-refinement)パターンを適用した最初のフレームワークです。
次の例は、マイクロフレームワークスタイルのルート定義で、アクションのロジックがルートパラメーターに埋め込まれています。
コンフィギュレーションメソッドである `modify()` から呼び出される `modifyRouter()` の中で、共有サービスとして定義されている `web_request` や `web_response`、および `web_router` サービスにアクセスできます。
これらのサービスを使って `blog.read` という名前のルートを追加し、アクションのコードをクロージャーとして埋め込んでいます。

```php
<?php
namespace Aura\Web_Project\_Config;

use Aura\Di\Config;
use Aura\Di\Container;

class Common extends Config
{
    // ...

    public function modifyRouter(Container $di)
    {
        $request = $di->get('web_request');
        $response = $di->get('web_response');

        $router = $di->get('web_router');
        $router
            ->add('blog.read', '/blog/read/{id}')
            ->addValues(array(
                'action' => function ($id) use ($request, $response) {
                    $content = "Reading blog post $id";
                    $response->content->set(htmlspecialchars(
                        $content, ENT_QUOTES|ENT_SUBSTITUTE, 'UTF-8'
                    ));
                }
            ));
    }

    // ...
}
?>
```

PHPのビルトインサーバーを起動してアプリケーションを実行できます...

    cd {$PROJECT_PATH}
    php -S localhost:8000 -t web/

... Webブラウザで <http://localhost:8000/blog/read/88> にアクセスして、表示を確認してみてください。

#### 修正したマイクロフレームワークスタイル

先ほどの例では、コントローラーのロジックをルート内に記述していましたが、これをディスパッチャーへ分割するように修正してみましょう。

アクションのクロージャーを抜き出して、`blog.read` という名前のディスパッチャーへ移動させます。
元のルートには、対応するディスパッチャーの名前を `action` の値に設定します。

```php
<?php
namespace Aura\Web_Project\_Config;

use Aura\Di\Config;
use Aura\Di\Container;

class Common extends Config
{
    // ...

    public function modifyDispatcher(Container $di)
    {
        $request = $di->get('web_request');
        $response = $di->get('web_response');
        
        $dispatcher = $di->get('web_dispatcher');
        $dispatcher->setObject(
            'blog.read',
            function ($id) use ($request, $response) {
                $content = "Reading blog post $id";
                $response->content->set(htmlspecialchars(
                    $content, ENT_QUOTES|ENT_SUBSTITUTE, 'UTF-8'
                ));
            }
        );
        
    }

    public function modifyRouter(Container $di)
    {
        $router = $di->get('web_router');
        $router
            ->add('blog.read', '/blog/read/{id}')
            ->addValues(array(
                'action' => 'blog.read',
            ));
    }

    // ...
}
?>
```

PHPのビルトインサーバーを起動してアプリケーションを実行できます...

    cd {$PROJECT_PATH}
    php -S localhost:8000 -t web/

... Webブラウザで <http://localhost:8000/blog/read/88> にアクセスして、表示を確認してみてください。

#### フルスタックスタイル

マイクロフレームワークスタイルからフルスタックスタイルへ移行できます（最初からフルスタックスタイルを採用することもできます）。

まず、`src/` ディレクトリ配下にアクションクラスを定義します。

```php
<?php
/**
 * {$PROJECT_PATH}/src/App/Actions/BlogReadAction.php
 */
namespace App\Actions;

use Aura\Web\Request;
use Aura\Web\Response;

class BlogReadAction
{
    public function __construct(Request $request, Response $response)
    {
        $this->request = $request;
        $this->response = $response;
    }
    
    public function __invoke($id)
    {
        $content = "Reading blog post $id";
        $this->response->content->set(htmlspecialchars(
            $content, ENT_QUOTES|ENT_SUBSTITUTE, 'UTF-8'
        ));
    }
}
?>
```

次に、プロジェクトのDIコンテナで_BlogReadAction_の構成を定義します。
プロジェクトの `config/Common.php` ファイルを開き、_BlogReadAction_のコンストラクタに `web_request` と `web_response` サービスを渡すようコンテナを設定します。

```php
<?php
namespace Aura\Web_Project\_Config;
 
use Aura\Di\Config;
use Aura\Di\Container;

class Common extends Config
{
    public function define(Container $di)
    {
        $di->set('logger', $di->lazyNew('Monolog\Logger'));

        $di->params['App\Actions\BlogReadAction'] = array(
            'request' => $di->lazyGet('web_request'),
            'response' => $di->lazyGet('web_response'),
        );
    }

    // ...
}
?>
```

`blog.read` ディスパッチャーには、遅延読み込みインスタンスとして_App\Actions\BlogReadAction_オブジェクトを指定します。

```php
<?php
namespace Aura\Web_Project\_Config;

use Aura\Di\Config;
use Aura\Di\Container;

class Common extends Config
{
    // ...

    public function modifyDispatcher(Container $di)
    {
        $dispatcher = $di->get('web_dispatcher');
        $dispatcher->setObject(
            'blog.read',
            $di->lazyNew('App\Actions\BlogReadAction')
        );
    }

    // ...
}
?>
```

... 最後に、`blog.read` アクションを指すようにルーターを変更します:

```php
<?php
namespace Aura\Web_Project\_Config;

use Aura\Di\Config;
use Aura\Di\Container;

class Common extends Config
{
    // ...

    public function modifyRouter(Container $di)
    {
        $router = $di->get('web_router');
        $router
            ->add('blog.read', '/blog/read/{id}')
            ->addValues(array(
                'action' => 'blog.read',
            ));
    }

    // ...
}
?>
```

PHPのビルトインサーバーを起動してアプリケーションを実行できます...

    cd {$PROJECT_PATH}
    php -S localhost:8000 -t web/

... Webブラウザで <http://localhost:8000/blog/read/88> にアクセスして、表示を確認してみてください。

#### 他のバリエーション

ここで紹介したのは、ルーターとディスパッチャーの一般的な組み合わせ方の一例です。
[他の組み合わせ方も多数あります](https://github.com/auraphp/Aura.Dispatcher/tree/develop-2#refactoring-to-architecture-changes)。
