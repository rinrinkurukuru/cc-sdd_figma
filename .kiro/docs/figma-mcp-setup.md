# Figma MCP設定ガイド

## 概要

このドキュメントは、cc-sdd（Claude Code Spec-Driven Development）プロジェクトにFigma MCP（Model Context Protocol）を統合するための設定手順を説明します。

Figma MCPを統合することで、以下が可能になります：
- Figmaデザインファイルから直接コンポーネント情報を取得
- デザイントークン（色、スペーシング、タイポグラフィ）の自動抽出
- デザインと実装の一貫性を保った仕様書の生成

---

## 推奨セットアップ方法

**Framelink MCP for Figma（推奨）**

GLipsが開発した`figma-developer-mcp`パッケージを使用します。このMCPサーバーは：
- すべてのFigmaプラン（無料版含む）で動作
- FigmaファイルURLを直接渡すだけで情報取得可能
- AI向けに最適化された簡潔なレスポンス
- WSL環境での動作確認済み

参考: https://github.com/GLips/Figma-Context-MCP

---

## セットアップ手順

### 前提条件
- Figmaアカウント（無料プランでも可）
- Claude Codeがインストールされていること
- Node.js および npm がインストール済みであること
  - `node --version` および `npx --version` で確認可能

### ステップ1: Figma Personal Access Tokenの取得

1. **Figmaにログイン**: https://www.figma.com/
2. **Settings を開く**:
   - 右上のプロフィールアイコンをクリック
   - 「Settings」を選択
3. **Personal Access Token を生成**:
   - 左メニューから「Personal access tokens」を選択
   - 「Create new token」をクリック
   - トークン名を入力（例: `cc-sdd-mcp`）
   - 「Generate token」をクリック
4. **トークンをコピー**:
   - 生成されたトークンをコピーして安全な場所に保存
   - ⚠️ このトークンは一度しか表示されません

**参考**: [Figma公式ドキュメント - Personal Access Tokens](https://help.figma.com/hc/en-us/articles/8085703771159-Manage-personal-access-tokens)

### ステップ2: プロジェクトのMCP設定ファイルを編集

**プロジェクトルートの`.mcp.json`を編集します。**

1. **設定ファイルを開く**:
   ```bash
   # プロジェクトルートの .mcp.json を編集
   code .mcp.json
   # または
   vi .mcp.json
   ```

2. **トークンを書き換える**:
   ```json
   {
     "mcpServers": {
       "figma-framelink": {
         "command": "npx",
         "args": [
           "-y",
           "figma-developer-mcp",
           "--figma-api-key=$FIGMA_PERSONAL_ACCESS_TOKEN",
           "--stdio"
         ]
       }
     }
   }
   ```

   **`$FIGMA_PERSONAL_ACCESS_TOKEN`の部分を、ステップ1で取得した実際のトークンに置き換えてください:**
   ```json
   "--figma-api-key=figd_xxxxxxxxxxxxxxxxxxxx",
   ```

   **重要**:
   - `$FIGMA_PERSONAL_ACCESS_TOKEN` の部分を実際のトークン文字列に置き換える
   - トークンは `figd_` で始まる形式
   - ダブルクォートを削除しないように注意

**編集例（置き換え前）**:
```json
"--figma-api-key=$FIGMA_PERSONAL_ACCESS_TOKEN",
```

**編集例（置き換え後）**:
```json
"--figma-api-key=figd_AbCdEf1234567890XyZ_abcdefgh12345678",
```

3. **ファイルを保存**:
   - VS Code: `Ctrl + S`
   - vi/vim: `:wq`

### ステップ3: Claude Codeを起動

1. **Claude Codeを起動**:
   ```bash
   claude code
   ```

   Claude Codeが`.mcp.json`を読み込み、Figma MCPサーバーを自動的に起動します

### ステップ4: 動作確認

**方法1: MCPサーバーの状態を確認（推奨）**

Claude Code内で以下を入力：
```
/mcp
```

これにより、接続されているMCPサーバーの一覧が表示されます。`figma-framelink`が表示されていれば設定成功です。

**方法2: 実際にFigmaファイルURLでテスト**

```
Figmaファイル https://www.figma.com/design/abc123/MyProject からコンポーネント情報を取得してください
```

成功すると、Figmaファイルの情報（コンポーネント、スタイル、ページ構造など）が返されます。

### 使い方

**FigmaファイルのURLを直接渡す:**

FigmaファイルのURLは以下の形式です：
```
https://www.figma.com/design/{FILE_KEY}/{FILE_NAME}
https://www.figma.com/file/{FILE_KEY}/{FILE_NAME}?node-id={NODE_ID}
```

**例:**
```
このFigmaデザインを実装してください: https://www.figma.com/design/abc123def456/MyDesignSystem
```

MCPサーバーがURL内のファイルキーやノードIDを自動的に抽出して、Figma APIから情報を取得します。

---

## トラブルシューティング

### MCPツールが表示されない

**1. 設定ファイルが存在するか確認**
```bash
# プロジェクトルートに .mcp.json が存在するか確認
ls -la .mcp.json
```

**2. JSON構文エラーをチェック**
```bash
# JSONの構文が正しいかチェック
cat .mcp.json | jq .
# エラーが出る場合は構文エラーがあります
```

**3. トークンの置き換えを確認**
```bash
# .mcp.json の内容を確認
cat .mcp.json
# "--figma-api-key=$FIGMA_PERSONAL_ACCESS_TOKEN" のままになっていないか確認
# 実際のトークン（figd_ で始まる文字列）に置き換わっているか確認
```

**4. Claude Codeを再起動**
```bash
# Claude Codeを終了してから再起動
claude code
```

### "Authentication failed" エラー

**トークンを確認:**
- Personal Access Tokenが有効か確認
- トークンの形式: `figd_` で始まる文字列
- `.mcp.json`内のトークンが正しいか再確認
- Figmaの設定ページでトークンが無効化されていないか確認

**トークンを再生成:**
1. Figmaの設定ページで古いトークンを削除
2. 新しいトークンを生成（ステップ1を参照）
3. `.mcp.json`を開いて`--figma-api-key=`の部分を新しいトークンに更新
4. Claude Codeを再起動

### "File not found" エラー

**Figmaファイルへのアクセス権を確認:**
- Figma Web版で該当ファイルを開けるか確認
- ファイルが共有されているか、自分のアカウントでアクセス可能か確認

**URLの形式を確認:**
```
正しい形式:
https://www.figma.com/design/abc123/ProjectName
https://www.figma.com/file/abc123/ProjectName

間違った形式:
https://figma.com/design/abc123/ProjectName (www が抜けている)
```

### npxコマンドが見つからない

**Node.jsがインストールされているか確認:**
```bash
# Node.jsとnpxのバージョン確認
node --version
npx --version

# インストールされていない場合はNode.jsをインストール
# 例: Ubuntu/Debian
sudo apt update
sudo apt install nodejs npm
```

### .mcp.json が読み込まれない

**Claude Codeの起動ディレクトリを確認:**
```bash
# .mcp.json が存在するディレクトリでClaude Codeを起動
cd /path/to/project
claude code
```

Claude Codeは起動時にカレントディレクトリの`.mcp.json`を読み込みます。プロジェクトルートで起動していることを確認してください。

---

## 次のステップ

Figma MCPの設定が完了したら、以下のcc-sddワークフローでFigmaデータを活用できます：

1. **ステアリング設定**:
   ```bash
   /kiro:steering-custom
   # "design-system" テンプレートを選択してFigmaファイル情報を記録
   ```

2. **要件定義**:
   ```bash
   /kiro:spec-requirements {feature}
   # UI関連の要件にFigmaデザイン参照を自動添付
   ```

3. **設計**:
   ```bash
   /kiro:spec-design {feature}
   # Figmaコンポーネント、デザイントークンを基にした設計書生成
   ```

詳細は各フェーズのドキュメントを参照してください。

---

## 参考リンク

**Framelink MCP for Figma:**
- [GitHubリポジトリ](https://github.com/GLips/Figma-Context-MCP)
- [Framelink公式ドキュメント](https://www.framelink.ai/docs/quickstart)
- [npmパッケージ](https://www.npmjs.com/package/figma-developer-mcp)

**Figma API:**
- [Figma API Documentation](https://www.figma.com/developers/api)
- [Personal Access Tokens管理](https://help.figma.com/hc/en-us/articles/8085703771159-Manage-personal-access-tokens)

**Model Context Protocol:**
- [MCP公式ドキュメント](https://github.com/modelcontextprotocol/specification)

---

## 追加情報: その他のFigma MCP選択肢

参考までに、他のFigma MCP実装も存在します（本ドキュメントでは推奨しませんが、特定の要件がある場合は検討してください）：

### Figma公式Dev Mode MCP
- **前提**: Figma Professional以上のプラン、Dev Mode機能
- **特徴**: Figma Desktopアプリと連携、トークン不要
- **制限**: 無料プランでは利用不可

### Anthropic公式 MCP Figma Server
- **パッケージ**: `@modelcontextprotocol/server-figma`
- **特徴**: Anthropic公式実装
- **制限**: 機能がシンプル、レスポンスが冗長

WSL環境での動作実績と使いやすさから、本ドキュメントでは **Framelink MCP for Figma** を推奨しています。
