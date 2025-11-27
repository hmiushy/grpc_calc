# gRPC Minimal Example (Python)

Python を用いた最小構成の gRPC サンプルプロジェクトです。クライアントがサーバに対して `Add(x, y)` を呼び出し、サーバが計算結果を返すシンプルな例を提供します。

## 📋 目次

- [gRPC とは](#grpc-とは)
- [概要](#概要)
- [機能](#機能)
- [要件](#要件)
- [セットアップ](#セットアップ)
- [使い方](#使い方)
- [プロジェクト構造](#プロジェクト構造)
- [補足情報](#補足情報)

## gRPC とは

**gRPC**（gRPC Remote Procedure Calls）は、Google が開発した高性能なオープンソースの RPC（Remote Procedure Call）フレームワークです。

### 主な特徴

- **高性能**: HTTP/2 をベースとしたバイナリプロトコルにより、REST API と比較して高速な通信が可能
- **型安全**: Protocol Buffers を使用した厳密な型定義により、コンパイル時にエラーを検出可能
- **多言語対応**: Python、Java、Go、C++、JavaScript、Rust など、多くの言語をサポート
- **ストリーミング**: 単方向・双方向のストリーミング通信をサポート
- **コード生成**: `.proto` ファイルから各言語のコードを自動生成

### Protocol Buffers との関係

gRPC は **Protocol Buffers（protobuf）** をデフォルトのシリアライゼーション形式として使用します。Protocol Buffers は、構造化データを効率的にシリアライズするための言語中立・プラットフォーム中立のフォーマットです。

### 使用場面

- マイクロサービス間の通信
- モバイルアプリとバックエンドサーバの通信
- リアルタイム通信が必要なアプリケーション
- 高性能が求められる内部 API

### REST API との違い

| 項目 | gRPC | REST API |
|------|------|----------|
| プロトコル | HTTP/2 | HTTP/1.1, HTTP/2 |
| データ形式 | Protocol Buffers（バイナリ） | JSON（テキスト） |
| ストリーミング | サポート | 制限あり |
| ブラウザサポート | 制限あり（gRPC-Web が必要） | 完全サポート |
| パフォーマンス | 高い | 中程度 |

## 概要

このプロジェクトは、gRPC を初めて学ぶ方のための最小構成のサンプルです。以下の要素を含んでいます：

- Protocol Buffers (`.proto`) ファイルの定義
- gRPC サーバの実装
- gRPC クライアントの実装
- 基本的な RPC 呼び出しの例

## 機能

- 2つの整数を受け取り、その合計を返す `Add` RPC メソッド
- シンプルで理解しやすいコード構造
- 最小限の依存関係

## 要件

- Python 3.7 以上
- [uv](https://github.com/astral-sh/uv)（パッケージ管理ツール）

## セットアップ

### 1. uv のインストール

uv は高速な Python パッケージ管理・仮想環境ツールです。

**Linux / macOS：**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Windows（PowerShell）：**

```powershell
irm https://astral.sh/uv/install.ps1 | iex
```

インストール確認：

```bash
uv --version
```

### 2. 仮想環境の作成と有効化

プロジェクトディレクトリで以下を実行します：

```bash
# 仮想環境の作成
uv venv .venv

# 仮想環境の有効化（Linux / macOS）
source .venv/bin/activate

# 仮想環境の有効化（Windows）
.venv\Scripts\activate
```

### 3. gRPC のインストール

仮想環境が有効な状態で、以下を実行します：

```bash
uv pip install grpcio grpcio-tools
```

### 4. コード生成

`.proto` ファイルから Python コードを生成します：

```bash
python -m grpc_tools.protoc \
  -I . \
  --python_out=. \
  --grpc_python_out=. \
  calc.proto
```

これにより以下のファイルが生成されます：

- `calc_pb2.py` - メッセージクラス
- `calc_pb2_grpc.py` - サービスとスタブクラス

## 使い方

### サーバの起動

ターミナルで以下を実行します：

```bash
python server.py
```

サーバが起動すると、以下のメッセージが表示されます：

```
Server started at :50051
```

### クライアントの実行

別のターミナルで以下を実行します：

```bash
python client.py
```

出力例：

```
Result: 30
```

## プロジェクト構造

```
calc/
├── calc.proto          # Protocol Buffers 定義ファイル
├── calc_pb2.py         # 生成されたメッセージクラス
├── calc_pb2_grpc.py    # 生成されたサービスとスタブクラス
├── server.py           # gRPC サーバ実装
├── client.py           # gRPC クライアント実装
├── .gitignore          # Git 除外設定
└── README.md           # このファイル
```

## 補足情報

### protoc について

Python 版 gRPC（`grpcio-tools`）には `protoc` が同梱されているため、Python の場合は OS に `protoc` を別途インストールする必要はありません。

Python 以外の言語（C++、Go、Rust など）で使用する場合は、OS 向けの `protoc` を追加でインストールする必要があります。

### .proto ファイルの詳細解説

`.proto` ファイルは、gRPC サービスのインターフェースとメッセージの構造を定義するファイルです。以下は `calc.proto` の詳細な説明です。

```proto
syntax = "proto3";
```

**`syntax = "proto3"`**: Protocol Buffers のバージョンを指定します。`proto3` は現在の推奨バージョンで、`proto2` よりもシンプルで使いやすい構文を提供します。

```proto
package calc;
```

**`package calc`**: 名前空間を定義します。これにより、同じプロジェクト内で異なるパッケージのメッセージやサービスを区別できます。生成される Python コードでは、このパッケージ名がモジュール名として使用されます。

```proto
service CalcService {
  rpc Add (AddRequest) returns (AddResponse);
}
```

**`service CalcService`**: gRPC サービスを定義します。サービスは複数の RPC メソッドを含むことができます。

**`rpc Add (AddRequest) returns (AddResponse)`**: 
- `Add`: RPC メソッド名。クライアントが呼び出す関数名になります。
- `AddRequest`: リクエストメッセージの型。クライアントからサーバに送信されるデータの構造を定義します。
- `AddResponse`: レスポンスメッセージの型。サーバからクライアントに返されるデータの構造を定義します。

```proto
message AddRequest {
  int32 x = 1;
  int32 y = 2;
}
```

**`message AddRequest`**: リクエストメッセージの定義です。

- `int32 x = 1`: 32ビット整数型のフィールド `x`。`= 1` はフィールド番号で、Protocol Buffers の内部で使用される一意の識別子です。1 から始まり、各フィールドに連番を割り当てます。
- `int32 y = 2`: 同様に、フィールド `y` を定義します。

フィールド番号は一度使用したら変更できません（後方互換性のため）。1-15 の番号は 1 バイトでエンコードされるため、頻繁に使用されるフィールドには小さい番号を割り当てるのが推奨されます。

```proto
message AddResponse {
  int32 result = 1;
}
```

**`message AddResponse`**: レスポンスメッセージの定義です。計算結果を格納する `result` フィールドを持ちます。

### サーバ実装の詳細解説

サーバ実装では、`.proto` ファイルで定義したサービスを実際に実装します。

```python
class CalcService(calc_pb2_grpc.CalcServiceServicer):
```

**`CalcServiceServicer`**: `calc_pb2_grpc` モジュールから生成された基底クラスです。このクラスを継承することで、`.proto` ファイルで定義した RPC メソッドを実装できます。

```python
def Add(self, request, context):
```

**`Add` メソッド**: `.proto` ファイルで定義した `rpc Add` に対応するメソッドです。

- **`request`**: `AddRequest` 型のオブジェクト。クライアントから送信されたリクエストデータが格納されています。`request.x` と `request.y` で値を取得できます。
- **`context`**: gRPC のコンテキストオブジェクト。メタデータの取得、ステータスコードの設定などに使用します。

```python
result = request.x + request.y
return calc_pb2.AddResponse(result=result)
```

リクエストから値を取得して計算し、`AddResponse` オブジェクトを作成して返します。この戻り値がクライアントに送信されます。

**サーバの起動処理**:

```python
server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
```

- `grpc.server()`: gRPC サーバインスタンスを作成します。
- `ThreadPoolExecutor(max_workers=10)`: リクエストを処理するスレッドプールを設定します。最大 10 個のワーカースレッドで並行処理が可能です。

```python
calc_pb2_grpc.add_CalcServiceServicer_to_server(CalcService(), server)
```

実装したサービスをサーバに登録します。

```python
server.add_insecure_port('[::]:50051')
```

- `[::]:50051`: IPv6 の全インターフェース（`::`）のポート 50051 でリッスンします。IPv4 の場合は `0.0.0.0:50051` も使用できます。
- `insecure_port`: 暗号化なしの接続です。本番環境では TLS を使用する `add_secure_port()` を推奨します。

### クライアント実装の詳細解説

クライアント実装では、サーバに接続して RPC メソッドを呼び出します。

```python
with grpc.insecure_channel('localhost:50051') as channel:
```

**`grpc.insecure_channel()`**: サーバへの接続チャネルを作成します。

- `'localhost:50051'`: 接続先のサーバアドレスとポート番号を指定します。
- `insecure_channel`: 暗号化なしの接続です。本番環境では `secure_channel()` を使用します。
- `with` 文を使用することで、チャネルの適切なクリーンアップが保証されます。

```python
stub = calc_pb2_grpc.CalcServiceStub(channel)
```

**`CalcServiceStub`**: サーバの RPC メソッドを呼び出すためのスタブ（代理オブジェクト）を作成します。このスタブを通じて、あたかもローカル関数のように RPC メソッドを呼び出すことができます。

```python
resp = stub.Add(calc_pb2.AddRequest(x=10, y=20))
```

**`stub.Add()`**: サーバの `Add` RPC メソッドを呼び出します。

- `calc_pb2.AddRequest(x=10, y=20)`: リクエストメッセージオブジェクトを作成し、`x=10`, `y=20` を設定します。
- この呼び出しは同期的に実行され、サーバからのレスポンスが返るまで待機します。
- 戻り値は `AddResponse` 型のオブジェクトです。

```python
print("Result:", resp.result)
```

レスポンスオブジェクトから `result` フィールドを取得して表示します。

### エラーハンドリング

実際のアプリケーションでは、以下のようなエラーハンドリングを追加することを推奨します：

**サーバ側**:
```python
def Add(self, request, context):
    try:
        result = request.x + request.y
        return calc_pb2.AddResponse(result=result)
    except Exception as e:
        context.set_code(grpc.StatusCode.INTERNAL)
        context.set_details(f'Error: {str(e)}')
        return calc_pb2.AddResponse()
```

**クライアント側**:
```python
try:
    resp = stub.Add(calc_pb2.AddRequest(x=10, y=20))
    print("Result:", resp.result)
except grpc.RpcError as e:
    print(f"RPC failed: {e.code()} - {e.details()}")
```
