# 🦅 Raven - macOS Terminal Emulator

## 基本仕様書 v0.2.0

**作成日**: 2025-12-25
**更新日**: 2025-12-25
**作成者**: むらた
**ステータス**: ドラフト

---

## 1. プロジェクト概要

### 1.1 コンセプト

Raven は macOS ネイティブの高機能ターミナルエミュレータ。
Git 統合、SSH 接続管理、シリアル通信を統合し、開発者の日常作業を効率化する。

### 1.2 名前の由来

- **Raven**(大烏): 黒い画面、賢さ、素早さを象徴
- Swift で書かれた「速い鳥」

### 1.3 ターゲット

- **プライマリ**: 自分専用ツール(学習 + 実用)
- **セカンダリ**: 将来的な OSS 公開も視野に

### 1.4 技術スタック

| レイヤー | 技術 |
|----------|------|
| 言語 | Swift 5.9+ |
| UI | SwiftUI + AppKit(必要に応じて) |
| PTY | Darwin POSIX API (`forkpty`, `posix_spawn`) |
| 最小対応OS | macOS 14.0 (Sonoma) 以降 |

### 1.5 設計原則

| 原則 | 説明 |
|------|------|
| Context Pattern | 依存性をまとめて管理・伝播 |
| HAL (Hardware Abstraction Layer) | プラットフォーム依存部分を Protocol で抽象化 |
| Component-based | 機能を独立したコンポーネントに分割 |
| Event-driven | コンポーネント間は疎結合なイベント通信 |
| Protocol Oriented Programming | Swift の Protocol を活用した柔軟な設計 |

---

## 2. 機能要件

### 2.1 コア機能(MVP - Phase 1)

#### 2.1.1 ターミナルエミュレーション

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| PTY 制御 | 疑似端末の作成・管理 | 必須 |
| シェル起動 | zsh/bash の子プロセス起動 | 必須 |
| ANSI エスケープ | 色(256色/TrueColor)、カーソル移動、画面制御 | 必須 |
| 入力処理 | 特殊キー(矢印、Ctrl+C、Cmd+V 等) | 必須 |
| 日本語対応 | IME 入力、UTF-8 表示 | 必須 |
| スクロールバック | 履歴バッファ(設定可能行数) | 必須 |
| フォント描画 | 等幅フォント、サイズ変更 | 必須 |
| Font Ligatures | プログラミング用リガチャ(Fira Code 等)対応 | 高 |

#### 2.1.2 ウィンドウ管理

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| タブ | 複数タブ、並び替え、タブ名編集 | 必須 |
| 分割ペイン | 水平/垂直分割、リサイズ、フォーカス移動 | 必須 |
| 新規ウィンドウ | Cmd+N で新規ウィンドウ | 必須 |
| フルスクリーン | macOS ネイティブ対応 | 必須 |

#### 2.1.3 安全機能(即追加)

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| マルチライン貼り付け確認 | 複数行ペースト時に確認ダイアログ表示 | 必須 |
| Secure Keyboard Entry | キーロガー対策(パスワード入力時等) | 必須 |

#### 2.1.4 UX 向上(即追加)

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| スムーズスクロール | アニメーション付きスクロール | 高 |
| OS テーマ連動 | macOS ダーク/ライトモード自動切替 | 高 |
| スクロールバー表示 | オプションで表示/非表示切替 | 高 |

---

### 2.2 Git 統合機能(Phase 2)

#### 2.2.1 ステータス表示

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| ブランチ名 | タブ/プロンプト領域に現在ブランチ表示 | 高 |
| Ahead/Behind | push/pull が必要な状態をアイコン表示 | 高 |
| 変更ファイル数 | staged/unstaged の件数表示 | 高 |
| コンフリクト検出 | マージコンフリクト時の警告表示 | 中 |
| Stash 数 | stash がある場合に件数表示 | 低 |

#### 2.2.2 操作支援

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| クイックコマンド | Cmd+G でGit操作パネル表示 | 中 |
| diff プレビュー | シンタックスハイライト付き差分表示 | 低 |
| ブランチ切替 | GUI でのブランチ選択 | 低 |

---

### 2.3 SSH 接続管理(Phase 2)

#### 2.3.1 接続管理

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| 接続先リスト | 保存済みホストの一覧管理 | 高 |
| クイック接続 | Cmd+K で Spotlight 風検索パネル | 高 |
| SSH Config 連携 | `~/.ssh/config` の読み込み・反映 | 高 |
| 自動接続 | IP/ホスト名入力で自動 ssh 実行 | 高 |
| 接続マクロ | 接続後の自動コマンド実行 | 中 |

#### 2.3.2 コンテキストメニュー

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| IP 検出 | 選択テキストが IP/ホスト名なら SSH 接続提案 | 高 |
| URL 検出 | URL クリックでブラウザ起動 | 中 |
| ファイルパス検出 | パスクリックで Finder/エディタ起動 | 中 |

#### 2.3.3 TeraTermライク機能

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| セッションログ | 自動ログファイル出力(日時/ホスト名別) | 高 |
| ブロードキャスト | 複数タブへの同時入力 | 中 |
| 接続状態表示 | タブに接続先ホスト名表示 | 高 |

#### 2.3.4 クリップボード・通知連携

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| OSC 52 Clipboard | リモートからローカルクリップボードへのコピー | 高 |
| Desktop Notifications | OSC 777/9 による通知(長時間コマンド完了等) | 中 |

---

### 2.4 シリアル接続(Phase 3)

#### 2.4.1 基本機能

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| ポート一覧 | `/dev/tty.*` のスキャン・リスト表示 | 高 |
| 接続設定 | ボーレート、データビット、パリティ、ストップビット | 高 |
| 接続/切断 | GUIボタン or ショートカット | 高 |
| 送信設定 | 改行コード選択(CR/LF/CRLF) | 中 |

#### 2.4.2 デバッグ支援

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| HEX 表示 | 送受信データの16進数表示モード | 中 |
| タイムスタンプ | 受信データへの時刻付与 | 中 |
| プリセット保存 | よく使う設定の保存・呼び出し | 低 |

---

### 2.5 ユーティリティ機能(Phase 2-3)

#### 2.5.1 セッション管理

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| セッション保存 | 開いてるタブ・ディレクトリを記憶 | 高 |
| セッション復元 | 起動時に前回状態を復元 | 高 |
| ワークスペース | 複数セッション構成の保存 | 低 |

#### 2.5.2 検索・履歴

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| 画面内検索 | Cmd+F でインクリメンタル検索 | 高 |
| 履歴検索 | Ctrl+R 強化版(fzf風) | 中 |
| ディレクトリブックマーク | Cmd+1~9 でよく使うパスへ移動 | 低 |

#### 2.5.3 表示・通知

| 機能 | 詳細 | 優先度 |
|------|------|--------|
| ベル通知 | macOS 通知センター連携 | 中 |
| プロセスモニタ | 実行中ジョブのサイドバー表示 | 低 |
| 転送プログレス | SCP/rsync の進捗表示 | 低 |

---

### 2.6 中期検討機能(Phase 4+)

#### 2.6.1 機能一覧(実装難易度・差別化度付き)

| 機能 | 詳細 | 実装難易度 | 差別化度 | 推奨Phase |
|------|------|:---------:|:--------:|:---------:|
| ハイパーリンク → エディタ起動 | ファイルパス:行番号 クリックで VSCode/vim で開く | ★★★☆☆ | ★★★★☆ | **4a** |
| Kitty Graphics Protocol | 高解像度画像インライン表示(ranger, yazi 等) | ★★★★☆ | ★★★☆☆ | **4a** |
| Terminal Inspector | エスケープシーケンスのリアルタイムデバッグUI | ★★★★☆ | ★★★★★ | **4b** |
| iTerm2 Inline Images | iTerm2 互換の画像表示(imgcat 対応) | ★★★☆☆ | ★★☆☆☆ | 4b |
| GPU Acceleration | Metal による GPU レンダリング | ★★★★★ | ★★☆☆☆ | 要検証 |
| アニメーションカーソル | カーソル移動のイージングアニメーション | ★★☆☆☆ | ★★★☆☆ | 5 |
| Sixel Graphics | レガシーな画像表示プロトコル | ★★★☆☆ | ★☆☆☆☆ | 5 |

#### 2.6.2 推奨実装順序

**Phase 4a (差別化・高価値)**
1. **ハイパーリンク → エディタ起動**: 実装難易度が中程度で、開発者の日常効率を大幅に向上
2. **Kitty Graphics Protocol**: 業界標準化が進んでおり、多くのツールが対応

**Phase 4b (差別化・独自機能)**
3. **Terminal Inspector**: Ghostty 以外ほぼ未実装、TUI 開発者に刺さる差別化機能
4. **iTerm2 Inline Images**: Kitty Protocol 実装後なら追加工数少

**Phase 5 (余裕があれば)**
5. **アニメーションカーソル**: 実装は簡単だが好みが分かれる
6. **Sixel Graphics**: レガシーで優先度低い

**要検証**
- **GPU Acceleration**: Core Text で性能問題が出た場合のみ検討

#### 2.6.3 見送り機能

| 機能 | 見送り理由 |
|------|------------|
| AI 統合(Warp風) | 差別化より実用性優先、後から追加可能 |
| Lua 設定 | Swift/JSON で十分、複雑化回避 |
| クロスプラットフォーム | macOS 専用で品質優先 |

---

## 3. 非機能要件

### 3.1 パフォーマンス

| 項目 | 目標値 |
|------|--------|
| 起動時間 | 0.5秒以内 |
| メモリ使用量 | 100MB 以下(タブ10個時) |
| CPU 使用率 | アイドル時 1% 以下 |
| 描画遅延 | 16ms 以下(60fps) |
| 大量出力 | `cat` 1GB ファイルでもハングしない |

### 3.2 安定性

| 項目 | 要件 |
|------|------|
| クラッシュ耐性 | 子プロセス死亡時も本体は継続 |
| メモリリーク | 長時間使用でも安定 |
| エラーハンドリング | 全操作で適切なエラー表示 |

### 3.3 セキュリティ

| 項目 | 要件 |
|------|------|
| SSH 鍵 | Keychain 連携、エージェント対応 |
| パスワード | 保存時は Keychain 使用 |
| 接続情報 | 暗号化保存 |
| Secure Keyboard Entry | キーロガー対策 |

---

## 4. UI/UX 設計

### 4.1 全体レイアウト

```
┌─────────────────────────────────────────────────────────────┐
│ [Tab1: ~/dev] [Tab2: server1] [Tab3: /dev/tty.usb] [+]     │ ← タブバー
├─────────────────────────────────────────────────────────────┤
│ ┌─────────────────────┬─────────────────────────────────┐  │
│ │                     │                                 │  │
│ │   Terminal Pane 1   │       Terminal Pane 2           │  │
│ │                     │                                 │  │
│ │   ~/dev/raven       │       user@server1:~            │  │
│ │   main ↑2 ↓1 M:3    │                                 │  │
│ │   $                 │       $                         │  │
│ │                     │                                 │  │
│ ├─────────────────────┴─────────────────────────────────┤  │
│ │                                                       │  │
│ │                  Terminal Pane 3                      │  │
│ │                  /dev/tty.usbserial-1420 @ 115200     │  │
│ │                                                       │  │
│ └───────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────┤
│ [Git: main] [↑2 ↓1] [M:3 U:1] │ SSH: server1 │ 115200 8N1 │ ← ステータスバー
└─────────────────────────────────────────────────────────────┘
```

### 4.2 Git ステータス表示

```
ブランチ名: main
↑2         : 2コミット ahead(push 必要)
↓1         : 1コミット behind(pull 必要)
M:3        : 3ファイル modified
U:1        : 1ファイル untracked
!          : コンフリクトあり(赤色)
S:2        : 2件 stash あり
```

### 4.3 カラースキーム・アクセシビリティ

#### 4.3.1 設計方針

| 方針 | 説明 |
|------|------|
| 外部ファイル定義 | テーマは JSON ファイルで外部定義、ユーザーカスタマイズ可能 |
| カラーユニバーサルデザイン | 色覚多様性(色盲・色弱)に配慮した配色 |
| 形状による補助 | 色だけでなくアイコン・形状でも情報を伝達 |
| プリセット同梱 | デフォルト、色覚多様性対応、人気テーマを同梱 |

#### 4.3.2 テーマファイル配置

```
~/.config/raven/themes/
├── default-dark.json       # デフォルトダーク(同梱)
├── default-light.json      # デフォルトライト(同梱)
├── colorblind-dark.json    # 色覚多様性対応ダーク(同梱)
├── colorblind-light.json   # 色覚多様性対応ライト(同梱)
└── *.json                  # ユーザー定義テーマ
```

#### 4.3.3 デフォルトカラー

| 要素 | ダークモード | ライトモード | 用途 |
|------|-------------|-------------|------|
| background | #1E1E1E | #FFFFFF | 背景 |
| foreground | #D4D4D4 | #1E1E1E | 文字 |
| cursor | #FFFFFF | #000000 | カーソル |
| selection | #264F78 | #ADD6FF | 選択範囲 |

#### 4.3.4 セマンティックカラー + アイコン

**色覚多様性対応**: 色だけでなく形状(アイコン)でも区別可能にする

| 意味 | デフォルト色 | 色覚対応色 | アイコン | 説明 |
|------|------------|-----------|:--------:|------|
| 成功 | #89D185 (緑) | #56B6C2 (青) | ✓ | 正常完了 |
| 警告 | #CCA700 (黄) | #D19A66 (オレンジ) | ⚠ | 注意が必要 |
| エラー | #F44747 (赤) | #C678DD (マゼンタ) | ✗ | エラー発生 |
| 情報 | #56B6C2 (シアン) | #56B6C2 (シアン) | ℹ | 情報表示 |

**Git ステータス表示**:

| 状態 | デフォルト色 | 色覚対応色 | アイコン | 表示例 |
|------|------------|-----------|:--------:|--------|
| Ahead | #89D185 (緑) | #56B6C2 (青) | ↑ | `↑2` |
| Behind | #CCA700 (黄) | #D19A66 (オレンジ) | ↓ | `↓1` |
| Conflict | #F44747 (赤) | #C678DD (マゼンタ) | ! | `!3` |
| Modified | #E5C07B (黄) | #D19A66 (オレンジ) | M | `M:5` |
| Untracked | #98C379 (緑) | #61AFEF (青) | U | `U:2` |
| Stash | #C678DD (紫) | #C678DD (紫) | S | `S:1` |

#### 4.3.5 ANSI 16色

| 色名 | ダーク | ライト | 色覚対応ダーク |
|------|--------|--------|---------------|
| Black | #000000 | #000000 | #000000 |
| Red | #CD3131 | #CD3131 | #C678DD |
| Green | #0DBC79 | #0DBC79 | #56B6C2 |
| Yellow | #E5E510 | #E5E510 | #D19A66 |
| Blue | #2472C8 | #2472C8 | #2472C8 |
| Magenta | #BC3FBC | #BC3FBC | #BC3FBC |
| Cyan | #11A8CD | #11A8CD | #11A8CD |
| White | #E5E5E5 | #E5E5E5 | #E5E5E5 |
| Bright Black | #666666 | #666666 | #666666 |
| Bright Red | #F14C4C | #F14C4C | #E06C75 |
| Bright Green | #23D18B | #23D18B | #61AFEF |
| Bright Yellow | #F5F543 | #F5F543 | #E5C07B |
| Bright Blue | #3B8EEA | #3B8EEA | #3B8EEA |
| Bright Magenta | #D670D6 | #D670D6 | #D670D6 |
| Bright Cyan | #29B8DB | #29B8DB | #29B8DB |
| Bright White | #FFFFFF | #FFFFFF | #FFFFFF |

### 4.4 キーバインド

#### 基本操作

| キー | 動作 |
|------|------|
| Cmd+T | 新規タブ |
| Cmd+W | タブを閉じる |
| Cmd+N | 新規ウィンドウ |
| Cmd+, | 設定画面 |
| Cmd+Q | アプリ終了 |

#### タブ/ペイン操作

| キー | 動作 |
|------|------|
| Cmd+1~9 | タブ切替 |
| Cmd+Shift+[ / ] | 前/次のタブ |
| Cmd+D | 垂直分割 |
| Cmd+Shift+D | 水平分割 |
| Cmd+Option+矢印 | ペイン間フォーカス移動 |
| Cmd+Shift+Enter | ペイン最大化トグル |

#### 検索・接続

| キー | 動作 |
|------|------|
| Cmd+F | 画面内検索 |
| Cmd+K | クイック接続(SSH/シリアル) |
| Cmd+G | Git 操作パネル |
| Ctrl+R | 履歴検索 |

#### 編集

| キー | 動作 |
|------|------|
| Cmd+C | コピー(選択時) / SIGINT(非選択時) |
| Cmd+V | ペースト |
| Cmd+Shift+V | プレーンテキストペースト |
| Cmd+A | 全選択 |
| Cmd++ / - | フォント拡大/縮小 |
| Cmd+0 | フォントサイズリセット |

---

## 5. データモデル

### 5.1 接続先設定(SSH)

```swift
struct SSHConnection: Codable, Identifiable {
    let id: UUID
    var name: String                    // 表示名
    var host: String                    // ホスト名 or IP
    var port: Int                       // ポート番号(デフォルト: 22)
    var user: String                    // ユーザー名
    var authMethod: AuthMethod          // 認証方式
    var identityFile: String?           // 秘密鍵パス
    var proxyCommand: String?           // ProxyCommand
    var localForwards: [PortForward]    // ローカルポートフォワード
    var remoteForwards: [PortForward]   // リモートポートフォワード
    var startupCommands: [String]       // 接続後自動実行コマンド
    var tags: [String]                  // タグ(検索用)
    var lastConnected: Date?            // 最終接続日時
}

enum AuthMethod: Codable {
    case password
    case publicKey
    case agent
    case keyboardInteractive
}

struct PortForward: Codable {
    var localPort: Int
    var remoteHost: String
    var remotePort: Int
}
```

### 5.2 接続先設定(シリアル)

```swift
struct SerialConnection: Codable, Identifiable {
    let id: UUID
    var name: String                    // 表示名
    var portPath: String                // /dev/tty.* パス
    var baudRate: Int                   // ボーレート
    var dataBits: DataBits              // データビット
    var parity: Parity                  // パリティ
    var stopBits: StopBits              // ストップビット
    var flowControl: FlowControl        // フロー制御
    var lineEnding: LineEnding          // 送信時改行コード
    var localEcho: Bool                 // ローカルエコー
    var hexMode: Bool                   // HEX 表示モード
    var tags: [String]                  // タグ
}

enum DataBits: Int, Codable { case five = 5, six = 6, seven = 7, eight = 8 }
enum Parity: Codable { case none, odd, even }
enum StopBits: Codable { case one, onePointFive, two }
enum FlowControl: Codable { case none, xonXoff, rtsCts }
enum LineEnding: String, Codable { case cr = "\r", lf = "\n", crlf = "\r\n" }
```

### 5.3 セッション状態

```swift
struct SessionState: Codable {
    var windows: [WindowState]
    var activeWindowIndex: Int
}

struct WindowState: Codable {
    var tabs: [TabState]
    var activeTabIndex: Int
    var frame: CGRect
}

struct TabState: Codable {
    var title: String
    var panes: [PaneState]
    var layout: PaneLayout
}

struct PaneState: Codable {
    var type: PaneType
    var workingDirectory: String?
    var connectionId: UUID?             // SSH or Serial の ID
    var scrollbackBuffer: Data?         // 履歴バッファ(オプション)
}

enum PaneType: Codable {
    case local                          // ローカルシェル
    case ssh                            // SSH 接続
    case serial                         // シリアル接続
}

enum PaneLayout: Codable {
    case single
    case horizontal([CGFloat])          // 各ペインの幅比率
    case vertical([CGFloat])            // 各ペインの高さ比率
    case grid([[CGFloat]])              // 複合レイアウト
}
```

### 5.4 設定

```swift
struct AppSettings: Codable {
    // 外観
    var theme: ThemeMode                // system / dark / light
    var themeName: String               // テーマファイル名(拡張子なし)
    var useColorblindTheme: Bool        // 色覚多様性対応テーマを使用
    var fontFamily: String
    var fontSize: CGFloat
    var lineHeight: CGFloat
    var cursorStyle: CursorStyle
    var cursorBlink: Bool
    var showScrollbar: Bool             // スクロールバー表示
    var smoothScrolling: Bool           // スムーズスクロール
    
    // ターミナル
    var scrollbackLines: Int
    var shell: String
    var shellArgs: [String]
    var environmentVariables: [String: String]
    
    // 動作
    var copyOnSelect: Bool
    var confirmClose: Bool
    var confirmMultilinePaste: Bool     // マルチライン貼り付け確認
    var restoreSession: Bool
    var secureKeyboardEntry: Bool       // Secure Keyboard Entry
    
    // ログ
    var autoLog: Bool
    var logDirectory: String
    var logFormat: LogFormat
    
    // Git
    var enableGitIntegration: Bool
    var gitStatusRefreshInterval: TimeInterval
    var showGitIcons: Bool              // Git ステータスにアイコン表示
    
    // 通知
    var bellAction: BellAction
    var notifyOnProcessComplete: Bool
    
    // アクセシビリティ
    var useIconsForStatus: Bool         // ステータス表示にアイコンを使用
}

enum ThemeMode: String, Codable { case system, dark, light }
enum CursorStyle: Codable { case block, underline, bar }
enum LogFormat: Codable { case plain, timestamped, html }
enum BellAction: Codable { case none, sound, notification, bounce }
```

---

## 6. アーキテクチャ

### 6.1 設計パターン概要

Raven は以下の設計パターンを組み合わせて実装する。

```
┌─────────────────────────────────────────────────────────────┐
│                    Context Pattern                          │
│  (依存性・設定・サービスをまとめて管理・伝播)                   │
│                                                             │
│  RavenContext: PTY, SSH, Serial, Git, Settings, EventBus   │
└─────────────────────────────────────────────────────────────┘
        │
        ▼ 注入(@Environment / 明示的渡し)
┌─────────────────────────────────────────────────────────────┐
│                   Component-based                           │
│  (機能を独立したコンポーネントに分割)                          │
│                                                             │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐           │
│  │ Terminal    │ │ SSH         │ │ Git         │           │
│  │ Component   │ │ Component   │ │ Component   │           │
│  └─────────────┘ └─────────────┘ └─────────────┘           │
└─────────────────────────────────────────────────────────────┘
        │                   │                 │
        ▼                   ▼                 ▼
┌─────────────────────────────────────────────────────────────┐
│                    Event-driven                             │
│  (コンポーネント間はイベント/メッセージで疎結合通信)             │
│                                                             │
│    PTYOutput ──▶ TerminalBuffer ──▶ Render                 │
│    GitStatusChanged ──▶ StatusBar                          │
│    SSHConnected ──▶ TabTitle                               │
└─────────────────────────────────────────────────────────────┘
        │
        ▼ 抽象化
┌─────────────────────────────────────────────────────────────┐
│                         HAL                                 │
│  (プラットフォーム依存部分を Protocol で抽象化)                │
│                                                             │
│    PTYProvider    SerialProvider    KeychainProvider        │
│         │               │                  │                │
│    DarwinPTY      DarwinSerial      SystemKeychain          │
│    MockPTY        MockSerial        MockKeychain            │
└─────────────────────────────────────────────────────────────┘
```

### 6.2 Context Pattern 実装

#### 6.2.1 RavenContext

```swift
/// アプリケーション全体の依存性を管理する Context
@Observable
final class RavenContext {
    
    // === HAL (Protocol で抽象化) ===
    let pty: PTYProvider
    let serial: SerialProvider
    let keychain: KeychainProvider
    
    // === Services ===
    let sshManager: SSHManager
    let gitMonitor: GitMonitor
    let sessionManager: SessionManager
    let logger: Logger
    
    // === Settings ===
    let settings: AppSettings
    
    // === Event Bus (Event-driven) ===
    let eventBus: EventBus
    
    // === Factory: テスト時はモック注入可能 ===
    init(
        pty: PTYProvider = DarwinPTY(),
        serial: SerialProvider = DarwinSerial(),
        keychain: KeychainProvider = SystemKeychain(),
        sshManager: SSHManager? = nil,
        gitMonitor: GitMonitor? = nil,
        sessionManager: SessionManager? = nil,
        logger: Logger = DefaultLogger(),
        settings: AppSettings = .default,
        eventBus: EventBus = EventBus()
    ) {
        self.pty = pty
        self.serial = serial
        self.keychain = keychain
        self.sshManager = sshManager ?? SSHManager(keychain: keychain)
        self.gitMonitor = gitMonitor ?? GitMonitor(eventBus: eventBus)
        self.sessionManager = sessionManager ?? SessionManager()
        self.logger = logger
        self.settings = settings
        self.eventBus = eventBus
    }
}
```

#### 6.2.2 SwiftUI への注入

```swift
@main
struct RavenApp: App {
    @State private var context = RavenContext()
    
    var body: some Scene {
        WindowGroup {
            MainWindowView()
                .environment(context)
        }
        .commands {
            RavenCommands(context: context)
        }
        
        Settings {
            SettingsView()
                .environment(context)
        }
    }
}

// 子 View での使用
struct TerminalView: View {
    @Environment(RavenContext.self) private var context
    
    var body: some View {
        // context.pty, context.settings など使用可能
    }
}
```

#### 6.2.3 Core 層での使用(SwiftUI 非依存)

```swift
/// Core 層のコンポーネントは明示的に Context を受け取る
protocol TerminalComponent {
    func setup(context: RavenContext)
    func teardown()
}

final class TerminalEmulator: TerminalComponent {
    private var context: RavenContext!
    private var ptySession: PTYSession?
    
    func setup(context: RavenContext) {
        self.context = context
    }
    
    func spawn(shell: String) async throws {
        ptySession = try await context.pty.spawn(
            shell: shell,
            args: context.settings.shellArgs
        )
    }
    
    func teardown() {
        ptySession?.terminate()
    }
}
```

### 6.3 HAL (Hardware Abstraction Layer)

#### 6.3.1 PTY Provider

```swift
/// PTY 操作の抽象化
protocol PTYProvider: Sendable {
    func spawn(shell: String, args: [String], environment: [String: String]) async throws -> PTYSession
}

protocol PTYSession: AnyObject, Sendable {
    var output: AsyncStream<Data> { get }
    func write(_ data: Data) async throws
    func resize(cols: Int, rows: Int) async throws
    func terminate()
}

/// macOS 実装
final class DarwinPTY: PTYProvider {
    func spawn(shell: String, args: [String], environment: [String: String]) async throws -> PTYSession {
        // forkpty() を使った実装
    }
}

/// テスト用モック
final class MockPTY: PTYProvider {
    var spawnHandler: ((String, [String]) async throws -> PTYSession)?
    
    func spawn(shell: String, args: [String], environment: [String: String]) async throws -> PTYSession {
        guard let handler = spawnHandler else {
            return MockPTYSession()
        }
        return try await handler(shell, args)
    }
}
```

#### 6.3.2 Serial Provider

```swift
/// シリアルポート操作の抽象化
protocol SerialProvider: Sendable {
    func availablePorts() async -> [SerialPortInfo]
    func connect(config: SerialConnection) async throws -> SerialSession
}

protocol SerialSession: AnyObject, Sendable {
    var input: AsyncStream<Data> { get }
    func write(_ data: Data) async throws
    func disconnect()
}

struct SerialPortInfo: Sendable {
    let path: String        // /dev/tty.usbserial-1420
    let name: String        // USB Serial Port
    let vendorId: Int?
    let productId: Int?
}

/// macOS 実装
final class DarwinSerial: SerialProvider {
    func availablePorts() async -> [SerialPortInfo] {
        // IOKit を使ったポートスキャン
    }
    
    func connect(config: SerialConnection) async throws -> SerialSession {
        // termios を使った接続
    }
}
```

#### 6.3.3 Keychain Provider

```swift
/// Keychain 操作の抽象化
protocol KeychainProvider: Sendable {
    func save(key: String, data: Data, service: String) async throws
    func load(key: String, service: String) async throws -> Data?
    func delete(key: String, service: String) async throws
}

/// macOS 実装
final class SystemKeychain: KeychainProvider {
    func save(key: String, data: Data, service: String) async throws {
        // Security.framework を使った実装
    }
    // ...
}
```

### 6.4 Event-driven 通信

#### 6.4.1 EventBus

```swift
/// 型安全なイベントバス
final class EventBus: @unchecked Sendable {
    private let subject = PassthroughSubject<any RavenEvent, Never>()
    private var cancellables = Set<AnyCancellable>()
    
    func publish<E: RavenEvent>(_ event: E) {
        subject.send(event)
    }
    
    func subscribe<E: RavenEvent>(
        _ eventType: E.Type,
        handler: @escaping (E) -> Void
    ) -> AnyCancellable {
        subject
            .compactMap { $0 as? E }
            .sink(receiveValue: handler)
    }
}

/// イベントプロトコル
protocol RavenEvent: Sendable {}

// 具体的なイベント
struct PTYOutputEvent: RavenEvent {
    let sessionId: UUID
    let data: Data
}

struct GitStatusChangedEvent: RavenEvent {
    let directory: String
    let status: GitStatus
}

struct SSHConnectedEvent: RavenEvent {
    let connectionId: UUID
    let host: String
}
```

#### 6.4.2 使用例

```swift
// Git モニターがステータス変更を通知
class GitMonitor {
    private let eventBus: EventBus
    
    func checkStatus(directory: String) async {
        let status = await fetchGitStatus(directory)
        eventBus.publish(GitStatusChangedEvent(
            directory: directory,
            status: status
        ))
    }
}

// ステータスバーが購読
class StatusBarViewModel {
    private var cancellable: AnyCancellable?
    
    func subscribe(eventBus: EventBus) {
        cancellable = eventBus.subscribe(GitStatusChangedEvent.self) { [weak self] event in
            self?.updateGitStatus(event.status)
        }
    }
}
```

### 6.5 モジュール構成

```
Raven/
├── App/
│   ├── RavenApp.swift              # エントリーポイント
│   ├── RavenContext.swift          # Context Pattern 実装
│   └── AppDelegate.swift           # AppKit ブリッジ
│
├── Core/
│   ├── HAL/                        # Hardware Abstraction Layer
│   │   ├── PTY/
│   │   │   ├── PTYProvider.swift   # Protocol 定義
│   │   │   ├── DarwinPTY.swift     # macOS 実装
│   │   │   └── MockPTY.swift       # テスト用モック
│   │   │
│   │   ├── Serial/
│   │   │   ├── SerialProvider.swift
│   │   │   ├── DarwinSerial.swift
│   │   │   └── MockSerial.swift
│   │   │
│   │   └── Keychain/
│   │       ├── KeychainProvider.swift
│   │       ├── SystemKeychain.swift
│   │       └── MockKeychain.swift
│   │
│   ├── Terminal/
│   │   ├── TerminalEmulator.swift  # ANSI パーサー
│   │   ├── TerminalBuffer.swift    # 画面バッファ
│   │   ├── TerminalState.swift     # 状態管理
│   │   └── ANSIParser.swift        # エスケープシーケンス解析
│   │
│   ├── Events/
│   │   ├── EventBus.swift          # イベントバス
│   │   └── Events.swift            # イベント定義
│   │
│   └── SSH/
│       ├── SSHManager.swift        # SSH 接続管理
│       └── SSHConfigParser.swift   # ~/.ssh/config パーサー
│
├── Features/
│   ├── Git/
│   │   ├── GitMonitor.swift        # Git 状態監視
│   │   └── GitCommands.swift       # Git コマンド実行
│   │
│   ├── Session/
│   │   ├── SessionManager.swift    # セッション保存/復元
│   │   └── WorkspaceManager.swift  # ワークスペース管理
│   │
│   ├── Search/
│   │   ├── BufferSearch.swift      # 画面内検索
│   │   └── HistorySearch.swift     # 履歴検索
│   │
│   └── Security/
│       └── SecureKeyboardEntry.swift # Secure Keyboard Entry
│
├── Views/
│   ├── MainWindow/
│   │   ├── MainWindowView.swift    # メインウィンドウ
│   │   ├── TabBarView.swift        # タブバー
│   │   └── StatusBarView.swift     # ステータスバー
│   │
│   ├── Terminal/
│   │   ├── TerminalView.swift      # ターミナル描画
│   │   ├── TerminalTextView.swift  # テキスト描画(NSViewRepresentable)
│   │   └── PaneContainerView.swift # ペイン管理
│   │
│   ├── Panels/
│   │   ├── QuickConnectPanel.swift # クイック接続
│   │   ├── GitPanel.swift          # Git 操作
│   │   ├── SearchPanel.swift       # 検索
│   │   └── MultilinePasteConfirm.swift # マルチライン貼り付け確認
│   │
│   └── Settings/
│       ├── SettingsView.swift      # 設定画面
│       ├── GeneralSettings.swift   # 一般設定
│       ├── AppearanceSettings.swift# 外観設定
│       └── ConnectionsSettings.swift# 接続先管理
│
├── ViewModels/
│   ├── TerminalViewModel.swift     # ターミナル VM
│   ├── TabViewModel.swift          # タブ VM
│   └── ConnectionViewModel.swift   # 接続 VM
│
├── Models/
│   ├── SSHConnection.swift
│   ├── SerialConnection.swift
│   ├── SessionState.swift
│   └── AppSettings.swift
│
├── Services/
│   ├── NotificationService.swift   # 通知
│   ├── LoggingService.swift        # ログ出力
│   └── ThemeService.swift          # テーマ管理(OS 連動)
│
└── Utilities/
    ├── Extensions/
    ├── Constants.swift
    └── Helpers.swift
```

### 6.6 依存関係図

```
┌──────────────────────────────────────────────────────────────────┐
│                            App Layer                             │
│  RavenApp, RavenContext                                         │
└──────────────────────────────────────────────────────────────────┘
        │
        ▼
┌──────────────────────────────────────────────────────────────────┐
│                           Views Layer                            │
│  SwiftUI Views, ViewModels                                      │
│  @Environment(RavenContext.self) で Context を受け取る           │
└──────────────────────────────────────────────────────────────────┘
        │
        ▼
┌──────────────────────────────────────────────────────────────────┐
│                        Features Layer                            │
│  Git, Session, Search, Security                                 │
│  Context を明示的に受け取って動作                                 │
└──────────────────────────────────────────────────────────────────┘
        │
        ▼
┌──────────────────────────────────────────────────────────────────┐
│                          Core Layer                              │
│  Terminal, Events, SSH                                          │
│  ビジネスロジック、SwiftUI 非依存                                 │
└──────────────────────────────────────────────────────────────────┘
        │
        ▼
┌──────────────────────────────────────────────────────────────────┐
│                           HAL Layer                              │
│  PTYProvider, SerialProvider, KeychainProvider                  │
│  Protocol + 実装(Darwin / Mock)                                 │
└──────────────────────────────────────────────────────────────────┘
        │
        ▼
┌──────────────────────────────────────────────────────────────────┐
│                         Models Layer                             │
│  Data structures, Codable types                                 │
│  依存なし                                                        │
└──────────────────────────────────────────────────────────────────┘
```

### 6.7 外部ライブラリ(検討)

| ライブラリ | 用途 | 必須/オプション |
|-----------|------|----------------|
| SwiftTerm | ANSI パーサー参考 | 参考のみ |
| Sparkle | 自動アップデート | オプション |

**基本方針**: 可能な限り標準ライブラリのみで実装。HAL により外部依存は最小化。

---

## 7. 開発フェーズ

### Phase 1: MVP(基本ターミナル)

**期間目安**: 2-3週間

| タスク | 詳細 |
|--------|------|
| プロジェクト構築 | Xcode プロジェクト、ディレクトリ構成、Context 基盤 |
| HAL 実装 | PTYProvider, DarwinPTY, MockPTY |
| PTY 実装 | forkpty、プロセス管理 |
| ANSI パーサー | 基本エスケープシーケンス対応 |
| ターミナル描画 | 等幅フォント、カーソル、選択 |
| 入力処理 | キーボード、特殊キー、IME |
| タブ/ペイン | 基本的なマルチタブ、分割 |
| 基本設定 | フォント、カラー、シェル指定 |
| 安全機能 | マルチライン貼り付け確認、Secure Keyboard Entry |
| UX 向上 | スムーズスクロール、OS テーマ連動、スクロールバー |

### Phase 2: 差別化機能

**期間目安**: 3-4週間

| タスク | 詳細 |
|--------|------|
| Git 統合 | GitMonitor, ステータス表示、ブランチ/ahead/behind |
| SSH 管理 | SSHManager, 接続先保存、クイック接続、Config 連携 |
| コンテキストメニュー | IP 検出→SSH、URL 検出、ファイルパス→エディタ |
| セッション管理 | SessionManager, 保存/復元 |
| ログ機能 | 自動ログ出力 |
| 画面内検索 | BufferSearch, Cmd+F 検索 |
| OSC 52 Clipboard | リモートからローカルクリップボードへコピー |
| Desktop Notifications | OSC 777/9 による通知 |

### Phase 3: 拡張機能

**期間目安**: 2-3週間

| タスク | 詳細 |
|--------|------|
| シリアル接続 | SerialProvider, DarwinSerial, ポートスキャン |
| ブロードキャスト | 複数タブ同時入力 |
| 履歴検索 | HistorySearch, fzf 風検索 |
| ディレクトリブックマーク | Cmd+1~9 移動 |
| HEX モード | シリアルデバッグ用 |

### Phase 4a: 高価値機能

**期間目安**: 2-3週間

| タスク | 詳細 | 実装難易度 | 差別化度 |
|--------|------|:---------:|:--------:|
| パフォーマンス最適化 | 描画、メモリ | ★★★☆☆ | - |
| エラーハンドリング | 全機能の例外処理 | ★★☆☆☆ | - |
| テスト | ユニットテスト(Mock 活用)、UI テスト | ★★★☆☆ | - |
| ハイパーリンク → エディタ起動 | ファイルパス:行番号 → VSCode/vim | ★★★☆☆ | ★★★★☆ |
| Kitty Graphics Protocol | 高解像度画像インライン表示 | ★★★★☆ | ★★★☆☆ |

### Phase 4b: 差別化機能

**期間目安**: 2-3週間

| タスク | 詳細 | 実装難易度 | 差別化度 |
|--------|------|:---------:|:--------:|
| Terminal Inspector | エスケープシーケンスのリアルタイムデバッグUI | ★★★★☆ | ★★★★★ |
| iTerm2 Inline Images | iTerm2 互換の画像表示(imgcat 対応) | ★★★☆☆ | ★★☆☆☆ |
| ドキュメント | README、使い方 | ★☆☆☆☆ | - |

### Phase 5: 追加機能(余裕があれば)

**期間目安**: 1-2週間

| タスク | 詳細 | 実装難易度 | 差別化度 |
|--------|------|:---------:|:--------:|
| アニメーションカーソル | カーソル移動のイージング | ★★☆☆☆ | ★★★☆☆ |
| Sixel Graphics | レガシーな画像表示プロトコル | ★★★☆☆ | ★☆☆☆☆ |
| GPU Acceleration | Metal による GPU レンダリング(要検証) | ★★★★★ | ★★☆☆☆ |

---

## 8. 技術的課題・検討事項

### 8.1 PTY 実装

- Darwin の `forkpty()` を使用
- `posix_spawn` でシェル起動
- 非同期 I/O は `DispatchIO` or Swift Concurrency (`AsyncStream`)

### 8.2 ANSI パーサー

- xterm 互換を目標
- 256色 + TrueColor 対応
- CSI、OSC、DCS シーケンス対応

### 8.3 パフォーマンス

- Metal での GPU 描画は Phase 4 以降で検討
- 初期は Core Text ベースで実装
- スクロールバックは行単位で遅延読み込み

### 8.4 SwiftUI の制限

- 低レベル入力処理は NSViewRepresentable でラップ
- 高頻度更新は AppKit 側で処理
- `@Observable` マクロで状態管理を簡潔に

### 8.5 テスタビリティ

- HAL により全プラットフォーム依存をモック可能
- Context Pattern により依存性注入が容易
- イベントベースで副作用を分離

---

## 9. 参考資料

### 既存ターミナル

- [iTerm2](https://iterm2.com/) - 機能参考、tmux 統合
- [Alacritty](https://alacritty.org/) - パフォーマンス参考
- [WezTerm](https://wezfurlong.org/wezterm/) - 設定形式参考、Lua スクリプト
- [Ghostty](https://ghostty.org/) - Terminal Inspector、Kitty Graphics
- [Kitty](https://sw.kovidgoyal.net/kitty/) - Graphics Protocol

### 技術ドキュメント

- [XTerm Control Sequences](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html)
- [ANSI Escape Code](https://en.wikipedia.org/wiki/ANSI_escape_code)
- [SwiftTerm](https://github.com/migueldeicaza/SwiftTerm) - Swift 実装参考
- [Kitty Graphics Protocol](https://sw.kovidgoyal.net/kitty/graphics-protocol/)

### 設計パターン

- [pre-omusubi Architecture](https://github.com/TakumiOkayasu/pre-omusubi) - HAL + Component-based + Event-driven
- [Swift Protocol Oriented Programming](https://developer.apple.com/videos/play/wwdc2015/408/)

---

## 10. 改訂履歴

| バージョン | 日付 | 内容 |
|-----------|------|------|
| 0.1.0 | 2025-12-25 | 初版作成 |
| 0.2.0 | 2025-12-25 | Context Pattern、HAL 追加。即追加機能(安全/UX)、中期検討機能追加。アーキテクチャセクション大幅更新 |
| 0.2.1 | 2025-12-25 | Font Ligatures、OSC 52 Clipboard、Desktop Notifications、iTerm2 Inline Images、Sixel Graphics 追加 |
| 0.3.0 | 2025-12-25 | 中期検討機能に実装難易度・差別化度追加。Phase 4a/4b/5 に分割。開発フェーズ更新 |
| 0.4.0 | 2025-12-25 | カラースキームをアクセシビリティ対応に更新。外部テーマファイル、色覚多様性対応、アイコン補助表示を追加 |

---

## 付録A: 用語集

| 用語 | 説明 |
|------|------|
| PTY | Pseudo Terminal - 疑似端末 |
| ANSI | American National Standards Institute - 端末制御の標準規格 |
| CSI | Control Sequence Introducer - `ESC [` で始まる制御シーケンス |
| OSC | Operating System Command - `ESC ]` で始まるコマンド |
| TrueColor | 24bit カラー(1677万色) |
| HAL | Hardware Abstraction Layer - ハードウェア抽象化層 |
| Context Pattern | 依存性をまとめて管理・伝播するパターン |
| Kitty Graphics Protocol | ターミナル内画像表示の標準プロトコル |

---

## 付録B: コンテキストメニュー仕様

### 選択なし(右クリック)

```
┌─────────────────────┐
│ ペースト            │
│ ───────────────── │
│ 垂直分割            │
│ 水平分割            │
│ ───────────────── │
│ 新規タブ            │
│ タブを閉じる        │
└─────────────────────┘
```

### テキスト選択時

```
┌─────────────────────────┐
│ コピー                  │
│ ─────────────────────  │
│ "192.168.1.1" で検索    │
│ ─────────────────────  │
│ SSH 接続: 192.168.1.1   │  ← IP アドレス検出時
│ ブラウザで開く          │  ← URL 検出時
│ VSCode で開く           │  ← ファイルパス検出時
│ Finder で表示           │  ← ファイルパス検出時
└─────────────────────────┘
```

---

## 付録C: マルチライン貼り付け確認ダイアログ

```
┌─────────────────────────────────────────────┐
│ ⚠️ 複数行のテキストを貼り付けようとしています │
├─────────────────────────────────────────────┤
│                                             │
│  以下の 3 行が貼り付けられます:              │
│                                             │
│  ┌─────────────────────────────────────┐   │
│  │ cd /var/log                         │   │
│  │ grep -r "error" .                   │   │
│  │ rm -rf /tmp/cache                   │   │
│  └─────────────────────────────────────┘   │
│                                             │
│  □ 今後このダイアログを表示しない           │
│                                             │
│        [キャンセル]  [貼り付け]              │
└─────────────────────────────────────────────┘
```

---

**以上**
