# training_devcontainer_vuejs

## ディレクトリ構造

```text
training_devcontainer_vuejs/
├── .devcontainer/
│   ├── devcontainer.json
│   ├── docker-compose.yml
│   └── Dockerfile
└── app/
    ├── package.json
    ├── vite.config.js
    ├── index.html
    └── src/
        ├── App.vue
        └── main.js
```

---

## 1. ファイルの作成と記述

```bash
mkdir -p .devcontainer
mkdir -p app
touch .devcontainer/devcontainer.json .devcontainer/docker-compose.yml .devcontainer/Dockerfile
```

## 2. 各ファイル解説

### `.devcontainer/Dockerfile`

Node.jsの公式軽量イメージをベースにし，Vue.jsの開発サーバーが外部からアクセスできるようにポートを開放する準備をする．

```dockerfile
FROM node:20-alpine

# 作業ディレクトリの設定
WORKDIR /workspace

# 開発用ポートの開放
EXPOSE 5173
```

* **解説**: 
軽量な Alpine Linux 版の Node.js (v20) を使用する．
Vite（Vue.jsの標準ビルドツール）のデフォルトポートである `5173` を公開．

---

### `.devcontainer/docker-compose.yml`

Dockerfileをビルドし，コンテナを起動したままにする(停止させない)設定．

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - ..:/workspace
    command: sleep infinity
    ports:
      - "5173:5173"
    working_dir: /workspace/app
```

* **解説**:
  - `volumes`: 
    ホスト側のプロジェクトルート全体をコンテナ内の `/workspace` にマウント．
  - `command: sleep infinity`: 
    何もせず終了してしまうコンテナを常時起動状態を保持．
  - `working_dir`: 
    ターミナルがデフォルトで `/workspace/app` を開くように指定．

---

### `.devcontainer/devcontainer.json`

VS Codeに「この構成をコンテナとして開く」ことを指示する設定ファイル．

```json
{
  "name": "Vue.js Minimal Devcontainer",
  "dockerComposeFile": "docker-compose.yml",
  "service": "app",
  "workspaceFolder": "/workspace/app"
}
```

* **解説**:
  - `dockerComposeFile` と `service`: 
    どのDocker Composeサービスを開発環境として使うかを指定．
  - `今回 `"extensions":` は記載なし(拡張機能なし)．

---

## 3. アプリケーションの構築と起動手順

### 1. コンテナを開く

1. ターミナルで `training_devcontainer_vuejs` ディレクトリに移動する．

2. VS Codeを起動する．

```bash
code .
```

3. コマンドパレット (`Ctrl + Shift + P`) を開き， **Dev Containers: Reopen in Container**(コンテナで再度開く)を実行する．

※自動でコンテナがビルドされ，内部に接続される．

### 2. Vueプロジェクトの作成 (Vite使用)

コンテナ内のターミナル(VS Code内の統合ターミナルなど)で，Vue.jsの最小限のプロジェクトを現在のディレクトリ (`app/`) 直下に作成する．

```bash
npm create vite@latest . -- --template vue
```

途中で「Current directory is not empty...」と聞かれた場合は、そのまま `y` または Enter を押して進める．

テンプレが作成されたら， `app/src/App.vue` の中身を以下に書き換える．

```vue
<script setup>
// ここにJavaScript/TypeScriptを書きます（今回はシンプルなので空でOK）
</script>

<template>
  <main>
    <h1>ハローワールド</h1>
  </main>
</template>

<style scoped>
/* 必要に応じてCSSを書きます */
</style>
```

* Vite（ヴィート）とは:

現代のWeb開発で標準的に使われている超高速なフロントエンドのビルドツール(開発サーバ)．
Vueのコード(.vueファイルなど)はそのままではブラウザが直接読めないため，ブラウザが理解できる形に変換し，さらにコードを書き換えたら一瞬でブラウザに反映される(ホットリロード)仕組みを内蔵．
将来的にDBやバックエンド(API)と連携する際も，通信の振り分け(プロキシ設定)などを担当する土台になる．

### 3. 依存パッケージのインストールと起動

必要なパッケージをインストールし，開発サーバを起動する．
外部(ホストPCのブラウザ)からアクセスできるように `--host` オプションを付与する．

```bash
npm install
npm run dev -- --host
```

---

## 4. 動作確認

ブラウザで下記URLにアクセスする．

```text
http://localhost:5173
```
