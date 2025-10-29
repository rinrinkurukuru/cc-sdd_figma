# Figma MCP設定ガイド

## 概要

このドキュメントは、cc-sdd（Claude Code Spec-Driven Development）プロジェクトにFigma MCP（Model Context Protocol）を統合するための設定手順を説明します。

Figma MCPを統合することで、以下が可能になります：
- Figmaデザインファイルから直接コンポーネント情報を取得
- デザイントークン（色、スペーシング、タイポグラフィ）の自動抽出
- デザインと実装の一貫性を保った仕様書の生成

---

## 2つのセットアップ方法

Figma MCPには2つのセットアップ方法があります。プロジェクトの要件に応じて選択してください。

| 方法 | メリット | デメリット | 推奨ケース |
|------|---------|-----------|-----------|
| **方法A: Figma公式Dev Mode MCP** | アクセストークン不要、公式サポート、セットアップ簡単 | Professional以上のプラン必須、Dev Mode機能に特化 | 有料プラン利用者、Dev Mode中心のワークフロー |
| **方法B: Community Figma MCP** | 全プラン対応、すべてのFigmaファイルにアクセス可能 | アクセストークン必要、npmインストール必要 | 無料プラン利用者、幅広いAPI機能が必要 |

---

## 方法A: Figma公式Dev Mode MCP Server（推奨）

### 前提条件
- Figma Professional、Business、またはEnterpriseプラン
- Dev または Full seat
- Claude Code がインストールされていること
- Figma Desktop アプリ（最新版）

### ステップ1: Figma DesktopでMCPサーバーを有効化

1. **Figma Desktopアプリを開く**
2. **Figmaメニューを開く**（画面左上）
3. **Preferences（環境設定）を選択**
4. **"Enable Dev Mode MCP Server" を有効化**

画面下部に以下のメッセージが表示されます：
```
Server enabled and running at http://127.0.0.1:3845/sse
```

### ステップ2: Claude Code MCP設定

以下のコマンドでMCPサーバーを追加：

```bash
claude mcp add --transport sse figma-dev-mode-mcp-server http://127.0.0.1:3845/sse
```

**または、設定ファイルを直接編集:**

**Linux/macOS:** `~/.config/Claude/claude_desktop_config.json`
**Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "figma-dev-mode": {
      "url": "http://127.0.0.1:3845/sse"
    }
  }
}
```

### ステップ3: 動作確認

1. Claude Codeを再起動
2. `/tools` コマンドを実行
3. 以下のツールが表示されることを確認：
   - `mcp__figma-dev-mode__get_code`
   - `mcp__figma-dev-mode__get_variable_defs`
   - `mcp__figma-dev-mode__get_code_connect_map`
   - `mcp__figma-dev-mode__get_image`

### 使い方

**選択ベース:**
1. Figma Desktopでフレームを選択
2. Claude Codeで「選択したフレームを実装してください」と指示

**リンクベース:**
1. Figmaでフレームリンクをコピー
2. Claude CodeにURLを渡して「このデザインを実装してください」と指示

---

## 方法B: Community Figma MCP Server

### 前提条件
- Figmaアカウント（無料プランでも可）
- Claude Code がインストールされていること
- Node.js 18以上

### ステップ1: Figma Personal Access Tokenの取得

1. **Figmaにログイン**: https://www.figma.com/
2. **Settings を開く**:
   - 右上のプロフィールアイコンをクリック
   - 「Settings」を選択
3. **Personal Access Token を生成**:
   - 左メニューから「Personal access tokens」を選択
   - 「Generate new token」をクリック
   - トークン名を入力（例: `cc-sdd-mcp`）
   - 必要な権限を選択:
     - ✅ File content (read)
     - ✅ File metadata (read)
   - 「Generate token」をクリック
4. **トークンをコピー**:
   - 生成されたトークンをコピーして安全な場所に保存
   - ⚠️ このトークンは一度しか表示されません

### ステップ2: Figma MCP Serverのインストール

```bash
npm install -g @modelcontextprotocol/server-figma
```

### ステップ3: Claude Code MCP設定

**Linux/macOS:** `~/.config/Claude/claude_desktop_config.json`
**Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "figma": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-figma"
      ],
      "env": {
        "FIGMA_PERSONAL_ACCESS_TOKEN": "YOUR_FIGMA_TOKEN_HERE"
      }
    }
  }
}
```

**重要**: `YOUR_FIGMA_TOKEN_HERE` をステップ1で取得したトークンに置き換えてください。

### ステップ4: 動作確認

1. Claude Codeを再起動
2. `/tools` コマンドを実行
3. 以下のツールが表示されることを確認：
   - `mcp__figma__get_file`
   - `mcp__figma__get_file_nodes`
   - `mcp__figma__get_component_sets`

### 使い方

**FigmaファイルキーとノードIDの確認:**

FigmaファイルのURLは以下の形式です：
```
https://www.figma.com/file/{FILE_KEY}/{FILE_NAME}?node-id={NODE_ID}
```

**例:**
```
https://www.figma.com/file/abc123def456/MyDesignSystem?node-id=10%3A234
```
- **FILE_KEY**: `abc123def456`
- **NODE_ID**: `10:234`

**テスト実行:**
```
FigmaファイルURL https://www.figma.com/file/abc123def456/MyDesignSystem からコンポーネント情報を取得してください
```

---

## トラブルシューティング

### 共通の問題

**MCPツールが表示されない:**
1. 設定ファイルのパスを再確認
2. JSON構文エラーをチェック
3. Claude Codeを完全に再起動（プロセスを終了してから起動）

### 方法A（Dev Mode MCP）固有

**サーバーが起動しない:**
- Figma Desktopアプリが最新版か確認
- Figma Desktopが起動しているか確認
- Professional以上のプランか確認

**接続できない:**
- `http://127.0.0.1:3845/sse` が正しく設定されているか確認
- ファイアウォールがローカルホストをブロックしていないか確認

### 方法B（Community MCP）固有

**"Authentication failed" エラー:**
- Personal Access Tokenが有効か確認
- 設定ファイルのトークンが正しいか確認
- Claude Codeを再起動

**"File not found" エラー:**
- FigmaファイルキーまたはノードIDが正しいか確認
- トークンに該当ファイルへのアクセス権限があるか確認
- Figmaでファイルにアクセスできることを確認

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

**方法A（Dev Mode MCP）:**
- [Figma Dev Mode MCP公式ガイド](https://help.figma.com/hc/ja/articles/32132100833559)

**方法B（Community MCP）:**
- [Figma API Documentation](https://www.figma.com/developers/api)
- [MCP Figma Server GitHub](https://github.com/modelcontextprotocol/servers/tree/main/src/figma)

**共通:**
- [Model Context Protocol (MCP) Documentation](https://github.com/modelcontextprotocol/specification)

---

**最終更新**: 2025-10-29
