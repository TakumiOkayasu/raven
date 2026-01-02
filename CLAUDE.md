# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## プロジェクト概要

**Raven** は macOS ネイティブのモダンターミナルエミュレータです。

| 項目 | 内容 |
|------|------|
| 言語 | Swift 5.9+ |
| UI | SwiftUI + AppKit (ターミナル描画部分) |
| 対象OS | macOS 14.0 (Sonoma) 以上 |
| アーキテクチャ | Context Pattern + HAL + Event-driven |

---

## ビルド・テスト

```bash
# ビルド
xcodebuild -project Raven.xcodeproj -scheme Raven -configuration Debug build

# 全テスト実行
xcodebuild test -project Raven.xcodeproj -scheme Raven

# 特定テストのみ実行
xcodebuild test -project Raven.xcodeproj -scheme Raven -only-testing:RavenTests/ANSIParserTests

# クリーンビルド
xcodebuild clean build -project Raven.xcodeproj -scheme Raven
```

**注意**: PTY/Serial アクセスには Sandbox OFF が必要。Xcode → Target → Signing & Capabilities → App Sandbox をオフにすること。

---

## 設計原則

### 依存性逆転の原則 (DIP) - 最重要

```
「実装はインターフェース(Protocol)に依存するが、インターフェースは実装に依存しない」
```

**必ず守ること**:

1. **Protocol 先行**: 実装クラスを書く前に Protocol を定義する
2. **import 禁止**: Core/Features 層から具体実装ファイルを import しない
3. **Context 経由**: 具体実装は `RavenContext` 経由でのみ注入する
4. **Mock 必須**: 全 Protocol に対応する Mock 実装を用意する

**禁止パターン**:
```swift
// ❌ 具体クラスを直接インスタンス化 (Views/Features 層で禁止)
let pty = DarwinPTY()

// ❌ 具体クラスの型を引数に
func setup(pty: DarwinPTY)

// ❌ Darwin/IOKit の import が Core/Features 層にある
import Darwin  // HAL/Darwin/ 以外で使用禁止
```

**正しいパターン**:
```swift
// ✅ Protocol 型で受け取る
func setup(pty: any PTYProvider)

// ✅ Context 経由でアクセス
@Environment(RavenContext.self) private var context
let session = try await context.pty.spawn(shell: "/bin/zsh", args: [], environment: [:])
```

---

## ディレクトリ構造

```
Raven/
├── App/
│   ├── RavenApp.swift           # エントリーポイント
│   └── RavenContext.swift       # 依存性注入コンテナ
│
├── Core/
│   ├── HAL/                     # Hardware Abstraction Layer
│   │   ├── Protocols/           # Protocol 定義のみ
│   │   │   ├── PTYProvider.swift
│   │   │   ├── SerialProvider.swift
│   │   │   └── KeychainProvider.swift
│   │   ├── Darwin/              # macOS 実装
│   │   │   ├── DarwinPTY.swift
│   │   │   ├── DarwinSerial.swift
│   │   │   └── SystemKeychain.swift
│   │   └── Mock/                # テスト用モック
│   │       ├── MockPTY.swift
│   │       ├── MockSerial.swift
│   │       └── MockKeychain.swift
│   │
│   ├── Terminal/
│   │   ├── TerminalBuffer.swift
│   │   ├── TerminalEmulator.swift
│   │   ├── ANSIParser.swift
│   │   └── TerminalState.swift
│   │
│   ├── Events/
│   │   ├── EventBus.swift
│   │   ├── RavenEvent.swift
│   │   └── *Events.swift
│   │
│   └── SSH/
│       └── SSHManager.swift
│
├── Features/
│   ├── Git/
│   ├── Session/
│   ├── Search/
│   ├── Security/
│   └── Theme/
│       ├── ThemeManager.swift
│       └── Theme.swift
│
├── Views/
│   ├── MainWindow/
│   ├── Terminal/
│   ├── Panels/
│   └── Settings/
│
├── ViewModels/
│
├── Models/
│   ├── AppSettings.swift
│   ├── SSHConnection.swift
│   ├── SerialConnection.swift
│   └── SessionState.swift
│
├── Services/
│   ├── ThemeService.swift
│   └── NotificationService.swift
│
├── Resources/
│   └── themes/                  # 同梱テーマ
│       ├── default-dark.json
│       ├── default-light.json
│       ├── colorblind-dark.json
│       └── colorblind-light.json
│
└── Utilities/
```

---

## レイヤー依存ルール

```
App Layer        → 具体実装をインスタンス化、Protocol 型で Context に格納
    ↓
Views Layer      → Protocol 型のみ参照、@Environment(RavenContext.self)
    ↓
Features Layer   → Protocol 型のみ参照、EventBus 経由で通信
    ↓
Core Layer       → Protocol 定義を含む、具体実装を参照しない
    ↓
HAL Layer        → Protocol + 実装 (Darwin/Mock)
    ↓
Models Layer     → 純粋なデータ構造、依存なし
```

---

## 命名規則

| 種類 | 規則 | 例 |
|------|------|-----|
| Protocol | `*Provider`, `*Delegate`, `*ing` | `PTYProvider`, `ThemeProviding` |
| 具体実装 | `Darwin*`, `System*`, `Mock*` | `DarwinPTY`, `MockKeychain` |
| イベント | `*Event` | `GitStatusChangedEvent` |
| エラー | `*Error` | `ThemeError`, `PTYError` |
| View | `*View` | `MainWindowView`, `TerminalView` |

---

## テーマシステム

### テーマファイル配置

```
# システムテーマ (読み取り専用)
/Applications/Raven.app/Contents/Resources/themes/

# ユーザーテーマ (読み書き可能)
~/.config/raven/themes/
```

### テーマ JSON 構造

```json
{
  "meta": { "name": "...", "author": "...", "version": "..." },
  "accessibility": { "colorblindFriendly": false },
  "appearance": "dark",
  "colors": {
    "terminal": { "background": "#1E1E1E", ... },
    "ansi": { "black": "#000000", ... },
    "semantic": { "success": { "background": "#89D185", "icon": "checkmark.circle.fill" }, ... },
    "git": { "ahead": { "color": "#89D185", "icon": "arrow.up", "text": "↑" }, ... }
  }
}
```

---

## アクセシビリティ

### 色覚多様性対応

- 赤緑の組み合わせを避ける
- 色 + アイコン + テキストの3要素で情報伝達
- 色覚対応テーマを同梱

### Git ステータスアイコン

| 状態 | SF Symbol | テキスト |
|------|-----------|---------|
| Ahead | `arrow.up` | ↑ |
| Behind | `arrow.down` | ↓ |
| Conflict | `exclamationmark.triangle` | ! |
| Modified | `pencil` | M |
| Untracked | `plus` | U |

---

## 実装時の注意点

### PTY 実装

- `forkpty()` を使用 (Darwin)
- 非同期 I/O は `AsyncStream` で実装
- シグナル処理に注意 (SIGCHLD)

### ANSI パーサー

- ステートマシンで実装
- 外部ライブラリ不使用
- TrueColor (24-bit) 対応必須

### SwiftUI + AppKit 連携

- ターミナル描画は `NSViewRepresentable` でラップ
- 高頻度更新部分は AppKit 直接使用 (Core Text)
- キー入力は `NSEvent` で処理

---

## よくある問題

| 問題 | 解決策 |
|------|--------|
| IME 入力が動かない | `insertText(_:replacementRange:)` を正しく実装、マークアップテキスト表示対応 |
| シリアルポートにアクセスできない | `/dev/tty.*` へのアクセス権限確認、`IOKit` でのポートスキャン時に権限確認 |

---

## 参考ドキュメント

| ファイル | 説明 |
|---------|------|
| `docs/Raven_Specification_v0.4.0.md` | 基本仕様書 |
| `docs/Raven_Detailed_Design.md` | 詳細設計書 |
| `docs/Raven_Screen_Design.md` | 画面設計書 |
| `docs/Raven_Implementation_Tasks.md` | 実装タスク分解 |

## 参考リンク

- [SwiftTerm](https://github.com/migueldeicaza/SwiftTerm) - ANSI パーサー参考
- [Ghostty](https://ghostty.org/) - Terminal Inspector 参考
- [Kitty Graphics Protocol](https://sw.kovidgoyal.net/kitty/graphics-protocol/)
- [VT100.net](https://vt100.net/) - エスケープシーケンス仕様

---

## Phase 別実装順序

### Phase 1: MVP
1. プロジェクト基盤 (Xcode, ディレクトリ)
2. Models 層
3. HAL Protocol → 具体実装 → Mock
4. Events 層
5. RavenContext
6. Terminal Core (Buffer, Parser, Emulator)
7. 基本 UI
8. 安全機能 (マルチライン確認, Secure Keyboard)

### Phase 2: 差別化
- Git 統合、SSH 管理、セッション管理
- OSC 52, Desktop Notifications

### Phase 3: 拡張
- シリアル接続、ブロードキャスト、履歴検索

### Phase 4a/4b: 高価値・差別化
- ハイパーリンク → エディタ起動
- Kitty Graphics Protocol
- Terminal Inspector

