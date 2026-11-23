<div align="center">

# ProxyNodeHub

### GitHub の無料ノードリポジトリを監視・集約するツール

[English](README.en.md) | [中文](README.md) | **日本語**

![ProxyNodeHub のメイン画面](images/main.png)

</div>

---

> ⭐ **ProxyNodeHub が役に立ったら、ぜひ Star をお願いします。それがこのプロジェクトを続けていくいちばんの励みになります。**

---

## 概要

ProxyNodeHub は、GitHub 上で活発に更新されている無料ノードのリポジトリを見つけるツールです。リポジトリの活動状況を分析し、公開されているノードの重複を取り除き、すぐに使えるサブスクリプションリンクを提供します。**搜索并分析** を 1 回クリックするだけで、残りの選別作業はすべて自動で行われます。

リポジトリを 1 つずつ開いて手作業で比較し、サブスクリプションを自分でつなぎ合わせる必要はもうありません。ProxyNodeHub は、最も熱心にメンテナンスされ、最も頻繁に更新されているリポジトリを見つけ出し、それらのノードを 1 つのエクスポートにまとめて重複を取り除きます。さらに、目立たず見落とされがちなリポジトリも掘り起こします。この分野では、アクセスが少ないほど負荷も低く、ノードが長持ちすることが多いからです。見落とされているリポジトリこそ、良いリポジトリであることがよくあります。

## 機能

この表は Windows クライアントについての説明です。Docker Web は同じ検出コアを共有し、ブラウザ UI、永続的なスケジュール、お気に入り、エクスポート、既存の subs-check サービスの任意の制御を追加します。WinForms や別のチェッカーエンジンは実行しません。

| | |
|---|---|
| **6 層の検出エンジン** | 機能ライブラリ → ハードコードされたマッピング → ファイルツリーのスキャン → 一般的なパスの探索 → README の解析 → フォールバック候補 |
| **並列検索** | GitHub の REST API に対して 8 つのキーワードクエリを並列に発行 |
| **活動スコア** | コミット頻度、規則性、自動化、目立たなさに基づく 100 点満点のモデル |
| **ノードの重複排除** | `server:port` をキーとして、1 つのサブスクリプションエクスポートにまとめます |
| **4 つの処理モード** | 🚀 ターボ / ⚖️ 標準 / 🔍 詳細 / 🐢 互換。モードごとに並列数が異なります |
| **自動プロキシ** | 13 の高速化ミラー、自動の遅延テスト、フェイルオーバー、5 分間の結果キャッシュ |
| **検索履歴フィルター** | 最近検索したリポジトリは自動的にスキップされます（1〜30 日で調整可能） |
| **お気に入り** | 専用のビューで、再起動後も保持されます |
| **機能ライブラリ** | うまくいったパスを学習し、次回の実行を速くします |
| **永続ログ** | 保持期間を設定でき、ワンクリックで消去できるローテーションログ |

## 動作要件

- **自己完結型ビルド** — Windows 10 以降（x64）。ほかに何もインストールする必要はありません。
- **フレームワーク依存ビルド** — Windows 10 以降（x64）と [.NET 8 Desktop Runtime](https://dotnet.microsoft.com/download/dotnet/8.0)。

## はじめに

1. [Releases](../../releases) からビルドを入手します。前提条件なしで使いたい場合は `ProxyNodeHub_v0.0.1_self-contained.zip` を選んでください。
2. 任意の場所に解凍し、`ProxyNodeHub.exe` を実行します。
3. **F5** を押す（または 搜索并分析 をクリックする）と、検索と分析が始まります。

GitHub トークンは任意です。デスクトップ版は現在、トークンを Windows の資格情報ストアではなく、EXE と同じ場所にある `settings.json` に平文で保存しています。このファイルをコミットしたり共有したりしないでください。検索には独自の API レート制限があります。GitHub のレスポンスヘッダーを基準にしてください。

## Docker Web

### Web 版の概要

このフォークは Windows クライアントを残したまま、常時稼働のサーバー、NAS デバイス、対応ルーター向けの Docker Web ワークスペースを追加しています。これは独立した ASP.NET Core サービスであり、リモートデスクトップではありません。操作はブラウザが担い、検出、スケジュール、永続化はブラウザを閉じた後もコンテナ内で動き続けます。

| エディション | 想定用途 | 実装 |
|---|---|---|
| Windows クライアント | ローカルでの検出、エクスポート、ローカルのチェッカーカーネル管理 | `src/`、WinForms / .NET 8 |
| Docker Web | LAN 内で共有するワークスペース、スケジュールされた検出、結果の一元管理 | `web/`、ASP.NET Core / .NET 10。CI は現在 `linux/amd64` を公開 |
| 共有コア | GitHub 検索、スコアリング、サブスクリプションの検出、解析、学習 | `core/`、両方のホストで再利用 |

6 つのページで、検出、お気に入り、サブスクリプションのチェック、実行履歴、学習したパス、サービス設定を扱います。Web は、絞り込み・並べ替え、お気に入りや再チェックの一括操作、範囲を指定したエクスポート、GitHub Token とチェッカー設定のオンライン設定、永続的なスケジュールに対応しています。独自デザインのドロップダウン、チェックボックス、確認ダイアログは、温かみのある紙のような配色に合わせつつ、セマンティックな HTML とキーボード操作を保っています。

subs-check は**任意の独立したサービス**で、このイメージには同梱されていません。検出、お気に入り、ソースのエクスポートは subs-check なしでも動作します。接続すると、チェッカーのステータス、速度やメディアの結果、許可リストに載った設定、ログ、成果物のダウンロードが追加されます。`/subscriptions.txt` はチェッカー向けにソース URL を一覧にしたもので、**テスト済みノードのサブスクリプションではありません**。

Web はデスクトップクライアントと完全に同等ではありません。ローカルでのカーネルのインストールやアップグレード、チェッカー YAML の完全な編集、実行ごとの履歴の再投入は再現されていません。[機能の詳細なレビュー](docs/web-parity-review.md)を参照してください。別の管理 UI を開けることは、Web に統合された機能とはみなしません。

イメージは `linux/amd64` に対応し、.NET 10 ランタイムを含んでいます。ホスト側にランタイムは必要ありません。ARM64 のイメージは現在公開していません。

### Web のスクリーンショット（モックデータ）

これらは現在のフロントエンドのスクリーンショットで、分離したブラウザで API レスポンスを差し替えて撮影しています。リポジトリ、`example.com` / `example.test` のアドレス、ノード、速度、メディアの結果はすべて架空のものです。本番の API、実際のトークン、パスワード、非公開のサブスクリプションは一切使用していません。プレビューにあるループバックのフィード URL はスクリーンショット専用です。デプロイ時は、チェッカーから到達できるホストのアドレスを使ってください。

![架空のモックデータを表示した Docker Web の検出ワークスペース](images/web-discovery-mock.png)

<details>
<summary>サービス設定：検出スケジュール、GitHub Token、チェッカーの接続</summary>

![モックの設定と空のシークレット欄を表示した Docker Web の設定画面](images/web-settings-mock.png)

</details>

<details>
<summary>サブスクリプションのチェック：独立した subs-check サービスから得た模擬的なステータスと結果</summary>

![架空のノード、速度、メディアのラベルを表示した Docker Web のチェッカー画面](images/web-checker-mock.png)

</details>

### 取得と実行

[Docker Hub のイメージ](https://hub.docker.com/r/helloworldz1024/proxynodehub)を使います。サーバーにソースをチェックアウトしたり .NET をインストールしたりする必要はありません。対象のコミットの [Actions による公開](https://github.com/goodnightzsj/ProxyNodeHub/actions/workflows/build.yml)が成功していることを確認してから、イメージを取得します。

```sh
docker pull helloworldz1024/proxynodehub:latest
```

`latest` は直近にプッシュされたイメージを指し、必ずしも GitHub の現在のコミットと一致するとは限りません。本番環境で固定してアップグレードする場合は `sha-<full-commit>` を使ってください。ローカルでビルドする場合は、リポジトリのルートで `docker build -t proxynodehub:local .` を実行し、以下のイメージ名をそのタグに置き換えてください。サービスをビルドするのはルートの `Dockerfile` で、`tests/Dockerfile` ではありません。

この例では `192.168.31.122` と `/mnt/usb1-1/proxynodehub` を使っています。アドレスとパスはお使いのホストに合わせて変更してください。永続データは `/script` の外に置いてください。ディレクトリとパスワードファイルは、既存のパスワードを上書きしないように、最初に一度だけ用意します。

```sh
mkdir -p /mnt/usb1-1/proxynodehub/data /mnt/usb1-1/proxynodehub/secrets
test -e /mnt/usb1-1/proxynodehub/secrets/admin-password || \
  (umask 077; openssl rand -base64 -out /mnt/usb1-1/proxynodehub/secrets/admin-password 32)
chown -R 1654:1654 /mnt/usb1-1/proxynodehub/data /mnt/usb1-1/proxynodehub/secrets
chmod 750 /mnt/usb1-1/proxynodehub/data /mnt/usb1-1/proxynodehub/secrets
chmod 600 /mnt/usb1-1/proxynodehub/secrets/admin-password
```

コンテナを起動します。同じ名前のコンテナがすでにある場合は、古いほうの書き込みを止め、データを保全してから作り直してください。

```sh
docker run -d \
  --name proxynodehub --restart always \
  --read-only --tmpfs /tmp:rw,noexec,nosuid,size=64m \
  --cap-drop ALL --security-opt no-new-privileges \
  --memory 512m --cpus 1 --pids-limit 128 \
  --log-opt max-size=5m --log-opt max-file=2 \
  -p 192.168.31.122:8388:8080 \
  -p 172.17.0.1:8388:8080 \
  -e ADMIN_PASSWORD_FILE=/run/secrets/admin-password \
  -e GITHUB_TOKEN="${GITHUB_TOKEN:-}" \
  -e REFRESH_HOURS=6 -e REPO_COUNT=10 -e INACTIVE_DAYS=7 -e AUTO_REFRESH=true \
  -v /mnt/usb1-1/proxynodehub/data:/data \
  -v /mnt/usb1-1/proxynodehub/secrets/admin-password:/run/secrets/admin-password:ro \
  helloworldz1024/proxynodehub:latest
```

`http://192.168.31.122:8388` を開きます。2 つ目のポートバインドは、既存のチェッカーが使う Docker ブリッジのフィードアドレスを維持するためのものです。ほかのホストでは、これらのアドレスを調整してください。この HTTP エンドポイントをインターネットに公開しないでください。信頼できないネットワークでは、信頼できる HTTPS リバースプロキシを使ってください。管理 API は、Cookie 認証、CSRF 対策、ログインのレート制限で保護されています。GitHub トークンはサーバー側でのみ使われ、ブラウザの状態に含まれることも、サブスクリプションのソースに送られることもありません。

サーバー側のファイルに保存されたパスワードでログインしてください。パスワードをチャットで共有したり、コミットしたりしないでください。`ADMIN_PASSWORD_FILE` の代わりに、ホストの環境変数から `ADMIN_PASSWORD`（12〜1024 文字）を渡すこともできます。両方を設定しないでください。パスワードファイルは UID 1654 から読み取れる必要があります。

新しいボリュームでは、検出がすぐに始まります。それ以降の実行は、前回の試行（失敗したものを含む）が終わってから 6 時間後に始まり、このタイミングは再起動後も引き継がれます。UI では、間隔（1〜24 時間）、候補数（1〜100）、活動日の範囲（1〜60 日）、並列数（1〜10）、自動スケジュールを変更できます。これらの環境変数が初期値になるのは新しいデータのときだけで、その後は UI で保存した設定が優先されます。実行は同時に 1 つだけで、キャンセルでき、上限は 15 分です。失敗や空の結果が、最後の有効なスナップショットを置き換えることはありません。一部だけ成功した検出は、そのことが明示されます。

`/data/state.json` には、設定、最大 200 件のお気に入り、現在のスナップショット、検索の記録、直近 20 回の実行（それぞれ最大 200 件のログ。保持期間は 1〜30 日）が保存されます。`features.json` には学習したパスが含まれます。`connections.protected` は、永続化された `keys/` のキーリングを使ってオンラインの接続設定を暗号化します。このキーリングはログインの保護にも使われます。ボリューム全体を保護し、バックアップしてください。キーリングを失うと復号できなくなり、逆にボリューム全体を持つ管理者は復号できます。1 つのボリュームにつき 1 つのコンテナにしてください。既存のヘッドレス版の `current.json` は、`state.json` がない場合に限り一度だけインポートされ、ロールバックに備えてそのまま残されます。

`/live` はプロセスの生存確認（Docker のヘルスチェック）です。`/health` は、有効なスナップショットができるまで、または新しいスナップショットがないまま 25 時間が経過すると 503 を返します。認証不要の `/subscriptions.txt` は公開サブスクリプションの URL を一覧にしたもので、統合したノードやトークンは含みません。到達できるその URL を、既存の subs-check の `sub-urls-remote` リストに追加してください。このフィードにイメージの変更や API キーは必要ありません。URI/Base64 のエクスポートはコアの `server:port` による重複排除を保ち、不完全な統合を作る代わりに、対応していない YAML の入力を拒否します。

GitHub Token とチェッカーの接続は、コンテナを作り直さなくても「服务设置」でオンラインで設定できます。それぞれの接続では、Web での上書き、環境変数の既定値、無効化のいずれかを明示的に使います。無効化した場合に、環境変数の認証情報へフォールバックすることはありません。保存したシークレットは書き込み専用で、ブラウザに保存されることはありません。チェッカーのアドレスを変更するには、新しいキーが必要です。GitHub の変更は次回の実行から適用されます。保存しても、ネットワークへの到達確認やタスクの開始は行われません。環境変数そのものや管理者パスワードを変更するには、引き続きコンテナの更新が必要です。新しい管理者パスワードを設定すると、古いセッションは無効になります。

チェッカー向けの任意の環境変数の既定値は、`SUBSCHECK_API_URL=http://192.168.31.122:8199`、`SUBSCHECK_API_KEY`、そしてブラウザ向けの `SUBSCHECK_WEB_URL=http://192.168.31.122:8199/admin` です。固定のプロトコルルートで、ステータスと結果、開始と停止、秘匿化したログ、バージョン、許可リストに載ったパラメーターの編集、ソースの明示的な追加、既存の Clash/Mihomo/Base64 の成果物のダウンロードを提供します。手動で追加した非公開のソースが公開フィードに入ることはありません。生の YAML はサーバーに残り、関係のないフィールドや未知のキーはそのまま保持されます。上流には compare-and-swap の API がないため、別の管理パネルで同時に編集しないでください。変更の中には、チェッカーの管理者による再起動が必要なものもあります。設定の全体、プロセスの管理、アップグレードは引き続き独立したチェッカー側で行い、Docker ソケットはマウントしません。

検出には、数値による絞り込み、双方向の並べ替え、絞り込みをまたいだ選択、コピー・お気に入り・再チェックの一括操作、範囲を指定したエクスポートが含まれます。ブラウザに保存されるのは、機密性のない表示設定だけです。明示的な探索では、最近の検索やお気に入りの除外を適用してソースを統合し、通常の検出ではスナップショット全体の意味を保ちます。固定ミラーに GitHub トークンが送られることはありません。実行ごとのログのダウンロードや消去、検索の記録の管理を行っても、スケジュールのタイムスタンプは保たれます。根拠や、真似すべきでないデスクトップ版の制限については、[機能の比較と設計](docs/web-parity.md)を参照してください。

アップグレードの前に、現在のイメージのダイジェストと実行時の設定を記録してください。データとパスワードのマウント、ポートバインド、セキュリティの制限を保ち、新しいコンテナを起動する前に古いほうの書き込みを止めてから、チェッカーのコンテナからヘルスとフィードへのアクセスを確認してください。検出は、認証済みの UI から更新してください。今回のパーサーの変更ではデータ形式の移行は行いません。ロールバックで、より新しいお気に入りや設定を上書きしないようにしてください。過去のバックアップ用コンテナは削除されている可能性があるため、固定のバックアップ名に頼らないでください。

### GitHub Actions

このワークフローは、Core/Web とフロントエンドをテストし、Windows の成果物を公開し、Linux amd64 のイメージをビルドして、HTTP と再起動のチェックを行います。Docker Hub にプッシュされるのは、テスト済みのそのイメージだけです。プルリクエストや、ほかのブランチでの手動ビルドがイメージを公開することはありません。Actions を有効にし、Settings → Secrets and variables → Actions で次の項目を設定してください。

| 名前 | 場所 | 値 |
|---|---|---|
| `DOCKERHUB_USERNAME` | **Variables → New repository variable** | `helloworldz1024` |
| `DOCKERHUB_TOKEN` | **Secrets → New repository secret** | 対象のイメージリポジトリへのプッシュ権限を持つ Docker Hub のトークン |

Variables に `DOCKERHUB_TOKEN` を登録しただけでは動作しません。ワークフローは `secrets.DOCKERHUB_TOKEN` を読み取ります。認証情報がない場合、公開は明示的に失敗します。トークンをコミットしたり、チャットで共有したりしないでください。漏れたトークンは失効させて、新しいものに差し替えてください。実行時の任意の `GITHUB_TOKEN` はこれとは関係がなく、Actions の設定を変えても、既存のコンテナの環境変数は更新されません。

main/master でのビルドが成功すると、`helloworldz1024/proxynodehub:latest` と `sha-<full-commit>` が公開されます。`v*` タグではバージョンと SHA のタグが公開されます。OCI ラベルで、ソースのリポジトリとリビジョンを識別できます。対応する Actions の実行が終わるのを待ってから、その SHA タグをデプロイし、レジストリのダイジェストを確認してください。以前の `web-20261008-d6db08ef` イメージは、Actions ではなく、コミットされていないローカルのソースからサーバーの Docker デーモンでビルドされ、直接プッシュされたものです。

### サブスクリプションの検証

チェッカーは、各実行の開始時に `/subscriptions.txt` を 1 回読み込みます。まず、フィードに到達できないのか、個々のダウンロード、解析、ノードの探索が失敗しているのかを切り分けてください。リンクごとのダウンロードの成功は、デバッグレベルのログにしか表示されないことがあります。コアは現在、YAML 全体を解析し、名前、種類、サーバー、有効なポートを持つトップレベルの `proxies` の項目だけを数えます。空のテンプレート、プロキシグループ名、不正な形式の YAML はノードとみなしません。これは認証情報や到達性を検証するものではありません。アップグレード後は、お気に入りや履歴を削除せずに検出を更新してください。

チェッカーの設定では、秒単位の `sub-urls-timeout` とサブスクリプション取得の並列数が、ミリ秒単位のノード探索の `timeout` とは別に設定できます。遅いリクエストを計測してから、たとえば 30 秒と並列数 10 のように調整してください。一時的なタイムアウトのあとで、有効なサブスクリプションを恒久的にブラックリストに入れないでください。設定の変更はチェッカーがアイドル状態のときに行う必要があり、変更しても実行は始まりません。

## ビルド

```bash
# self-contained single file (~68 MB, includes the runtime)
dotnet publish src/ProxyNodeHub.csproj -c Release -r win-x64 -p:SelfContained=true -o dist/self-contained

# framework-dependent (~3 MB, needs the .NET 8 runtime on the machine)
dotnet publish src/ProxyNodeHub.csproj -c Release -r win-x64 -p:SelfContained=false -o dist/framework-dependent
```

Windows Desktop ワークロードを含む .NET 8 SDK が必要です。

> **サイズについて:** 自己完結型ビルドはトリミングできません。WinForms では `PublishTrimmed` がサポートされていない（NETSDK1175）ため、.NET 8 の WinForms 単一ファイルパッケージでは約 65 MB が下限です。

## キーボードショートカット

| ショートカット | 動作 |
|---|---|
| `F5` | 検索の開始 / キャンセル |
| `Ctrl+C` | 最適なサブスクリプションリンクをコピー |
| `Ctrl+L` | 詳細パネルの表示を切り替え |
| `Ctrl+D` | お気に入りの表示を切り替え |
| `Ctrl+F` | フィルター欄にフォーカス |
| `Esc` | ダイアログを閉じる |

## 設定

ツールが学習した内容はすべてバイナリの外、`%AppData%\ProxyNodeHub\` の下に保存されます。

| ファイル / パス | 用途 |
|---|---|
| `known_repos.json` | EXE と同じ場所に同梱。既知のリポジトリからサブスクリプションのパスへのマッピング |
| `searched_repos.json` | 検索履歴。項目ごとに有効期限があります |
| `log.txt` | ローテーションログ。起動時に保持期間に合わせて切り詰められます |
| 機能ライブラリの JSON | 探索に成功したパス。L1 層で再利用されます |

## プロジェクト構成

依存関係の向きは `Desktop → Core ← Web` です。両方のホストが 1 つの検出パイプラインを共有しています。Web UI はプレーンな HTML/CSS/JavaScript で、フロントエンドのビルド依存関係はありません。ルートの Dockerfile がサービスをビルドし、`tests/Dockerfile` はビルドの検証だけを行います。

回帰チェックには .NET 10 SDK を使ってください。コアとデスクトップは引き続き .NET 8 をターゲットにしています。

```bash
dotnet run --project tests/ProxyNodeHub.Tests -c Release
node web/wwwroot/app.js --self-test
dotnet build src/ProxyNodeHub.csproj -c Release -p:EnableWindowsTargeting=true
docker build -f tests/Dockerfile --target verify -t proxynodehub-verify:local .
```

共有コアは現在、API の失敗を報告し、キャンセルを伝播し、デフォルトブランチを尊重し、同期した書き込みで機能のフィールドをアトミックに永続化します。書き込みに失敗した場合、新しいメモリ上の状態は公開されません。デスクトップの設定パスは変わっておらず、既知のマッピングは引き続きアプリと一緒に配布されます。

```
core/                       # net8.0 shared core; no WinForms / ASP.NET dependency
├── DiscoveryEngine.cs      # Shared queries and analysis pipeline
├── GitHubService.cs        # HTTP, desktop mirrors and server source restrictions
├── SubscriptionFinder.cs  # Six-layer subscription discovery
├── GitHubAnalyzer.cs       # Statistics and scoring
├── FeatureLibrary.cs       # Instance-owned, synchronized atomic persistence
├── NodeParser.cs           # Existing parsing and deduplication
├── KnownRepoLoader.cs      # Known subscription mappings
├── known_repos.json        # Single source, published with each host
└── Models.cs / AtomicFile.cs
src/                        # Windows UI, settings, proxies and speed tests
web/                        # ASP.NET API, scheduler and static browser UI
Dockerfile                  # Multi-stage, non-root Web service image
tests/ProxyNodeHub.Tests/    # Dependency-free Core and Web regression executable
tests/http-smoke.mjs         # Real HTTP authentication and persistence checks
tests/Dockerfile            # Core checks and Windows cross-build; NOT a service image
```

## 技術スタック

- .NET 8 上の C# / WinForms（`net8.0-windows`、`win-x64`）
- Windows 10 以降、x64
- Docker Web：ASP.NET Core / .NET 10、非 root の Alpine Linux イメージ。フロントエンドのビルド依存関係のない独自の HTML/CSS/JavaScript コントロール
- コアはノードの YAML の検証に YamlDotNet 18.1.0 を使います。デスクトップと Web はこの依存関係を共有し、Web はチェッカーの設定を保持するためにも使います

## 作者

**Zoyaya** — [github.com/wanvfx](https://github.com/wanvfx)

## ライセンス

[MIT License](LICENSE) の下で公開されています。
