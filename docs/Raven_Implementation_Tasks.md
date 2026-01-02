# 🦅 Raven - 実装タスク分解

## Claude Code 用実装ガイド v0.1.0

**作成日**: 2025-12-25
**対象**: 基本仕様書 v0.2.0

---

## 1. 設計原則

### 1.1 依存性逆転の原則 (Dependency Inversion Principle)

Raven の実装において、以下の原則を**厳守**する。

> **「実装はインターフェース(Protocol)に依存するが、インターフェースは実装に依存しない」**

```
✅ 正しい依存関係:
┌─────────────────┐     ┌─────────────────┐
│  TerminalView   │────▶│  PTYProvider    │ (Protocol)
└─────────────────┘     └─────────────────┘
                               ▲
                               │ 実装
                        ┌──────┴──────┐
                        │  DarwinPTY  │ (Concrete)
                        └─────────────┘

❌ 禁止される依存関係:
┌─────────────────┐     ┌─────────────────┐
│  TerminalView   │────▶│   DarwinPTY     │ (Concrete に直接依存)
└─────────────────┘     └─────────────────┘
```

### 1.2 具体的なルール

| ルール | 説明 |
|--------|------|
| **Protocol 先行** | 実装クラスを書く前に、必ず Protocol を定義する |
| **import 禁止** | Core/Features 層から具体実装ファイルを import しない |
| **Context 経由** | 具体実装は `RavenContext` 経由でのみ注入する |
| **Mock 必須** | 全 Protocol に対応する Mock 実装を用意する |
| **上位層は下位層を知らない** | Protocol は実装の詳細(Darwin API 等)を露出しない |

### 1.3 レイヤー間の依存ルール

```
┌──────────────────────────────────────────────────────────────────┐
│ App Layer (RavenApp, RavenContext)                              │
│ - 具体実装をインスタンス化し、Protocol 型として Context に格納    │
│ - ここだけが具体実装を知っている                                  │
└──────────────────────────────────────────────────────────────────┘
        │ Protocol 型で注入
        ▼
┌──────────────────────────────────────────────────────────────────┐
│ Views / ViewModels Layer                                        │
│ - Protocol 型のみ参照 (PTYProvider, SerialProvider 等)           │
│ - 具体実装の存在を知らない                                        │
└──────────────────────────────────────────────────────────────────┘
        │ Protocol 型で参照
        ▼
┌──────────────────────────────────────────────────────────────────┐
│ Features Layer (Git, Session, Search)                           │
│ - Protocol 型のみ参照                                            │
│ - EventBus 経由で疎結合通信                                      │
└──────────────────────────────────────────────────────────────────┘
        │ Protocol 型で参照
        ▼
┌──────────────────────────────────────────────────────────────────┐
│ Core Layer (Terminal, Events, SSH)                              │
│ - Protocol 定義を含む                                            │
│ - 具体実装を参照しない                                            │
└──────────────────────────────────────────────────────────────────┘
        │ Protocol 定義のみ
        ▼
┌──────────────────────────────────────────────────────────────────┐
│ HAL Layer (Protocol 定義 + 具体実装)                             │
│ - Protocol: PTYProvider, SerialProvider, KeychainProvider       │
│ - 実装: DarwinPTY, DarwinSerial, SystemKeychain                 │
│ - Mock: MockPTY, MockSerial, MockKeychain                       │
│ ※ Protocol と実装は同じ層だが、別ファイル・別ディレクトリ          │
└──────────────────────────────────────────────────────────────────┘
        │
        ▼
┌──────────────────────────────────────────────────────────────────┐
│ Models Layer                                                    │
│ - 純粋なデータ構造のみ                                            │
│ - 依存なし                                                       │
└──────────────────────────────────────────────────────────────────┘
```

### 1.4 ファイル配置ルール

```
Core/HAL/
├── Protocols/              # Protocol 定義のみ
│   ├── PTYProvider.swift
│   ├── SerialProvider.swift
│   └── KeychainProvider.swift
│
├── Darwin/                 # macOS 具体実装
│   ├── DarwinPTY.swift
│   ├── DarwinSerial.swift
│   └── SystemKeychain.swift
│
└── Mock/                   # テスト用モック
    ├── MockPTY.swift
    ├── MockSerial.swift
    └── MockKeychain.swift
```

**import ルール:**
```swift
// ✅ OK: Protocol ファイルを import
import RavenCore  // PTYProvider 等の Protocol が含まれる

// ❌ NG: 具体実装を直接 import
import DarwinPTY  // 禁止
```

---

## 2. 実装タスク一覧

### Phase 1: MVP(基本ターミナル)

#### Task 1.1: プロジェクト基盤構築

**目的**: Xcode プロジェクト作成、ディレクトリ構造、基本設定

```
【Claude Code での実装タスク】

1. Xcode プロジェクト作成
   - 目的: Raven.xcodeproj の作成
   - 手順:
     - macOS App テンプレート (SwiftUI lifecycle)
     - Bundle ID: com.yourname.Raven
     - Deployment Target: macOS 14.0
   - 注意点: Sandbox は OFF (PTY/Serial アクセスのため)

2. ディレクトリ構造作成
   - 目的: 仕様書 6.5 に準拠した構造
   - 手順:
     App/
     Core/
       HAL/
         Protocols/
         Darwin/
         Mock/
       Terminal/
       Events/
       SSH/
     Features/
       Git/
       Session/
       Search/
       Security/
     Views/
       MainWindow/
       Terminal/
       Panels/
       Settings/
     ViewModels/
     Models/
     Services/
     Utilities/
   - 注意点: 各ディレクトリに .gitkeep または空の Swift ファイル配置

3. SwiftLint 設定 (オプション)
   - 目的: コード品質維持
   - 手順: .swiftlint.yml 作成
```

---

#### Task 1.2: Models 層実装

**目的**: データ構造の定義(依存なし)

```
【Claude Code での実装タスク】

1. Models/AppSettings.swift
   - 目的: アプリ設定のデータ構造
   - 内容: 仕様書 5.4 の struct AppSettings
   - 注意点:
     - Codable 準拠
     - static let `default` で初期値提供
     - ThemeMode, CursorStyle 等の enum も同ファイルに

2. Models/SSHConnection.swift
   - 目的: SSH 接続設定
   - 内容: 仕様書 5.1 の struct
   - 注意点: AuthMethod, PortForward も含める

3. Models/SerialConnection.swift
   - 目的: シリアル接続設定
   - 内容: 仕様書 5.2 の struct
   - 注意点: DataBits, Parity 等の enum も含める

4. Models/SessionState.swift
   - 目的: セッション状態保存用
   - 内容: 仕様書 5.3 の struct
   - 注意点: PaneType, PaneLayout enum も含める
```

---

#### Task 1.3: HAL Protocol 定義

**目的**: インターフェース定義(実装に依存しない)

```
【Claude Code での実装タスク】

1. Core/HAL/Protocols/PTYProvider.swift
   - 目的: PTY 操作の抽象化
   - 内容:
     ```swift
     protocol PTYProvider: Sendable {
         func spawn(shell: String, args: [String], environment: [String: String]) async throws -> PTYSession
     }
     
     protocol PTYSession: AnyObject, Sendable {
         var output: AsyncStream<Data> { get }
         func write(_ data: Data) async throws
         func resize(cols: Int, rows: Int) async throws
         func terminate()
         var isRunning: Bool { get }
     }
     ```
   - 注意点:
     - Darwin API への参照なし
     - Sendable 準拠で Swift Concurrency 対応
     - エラー型も Protocol で定義

2. Core/HAL/Protocols/SerialProvider.swift
   - 目的: シリアルポート操作の抽象化
   - 内容:
     ```swift
     protocol SerialProvider: Sendable {
         func availablePorts() async -> [SerialPortInfo]
         func connect(config: SerialConnection) async throws -> SerialSession
     }
     
     protocol SerialSession: AnyObject, Sendable {
         var input: AsyncStream<Data> { get }
         func write(_ data: Data) async throws
         func disconnect()
     }
     
     struct SerialPortInfo: Sendable, Identifiable {
         let id: String  // path
         let path: String
         let name: String
         let vendorId: Int?
         let productId: Int?
     }
     ```
   - 注意点: IOKit への参照なし

3. Core/HAL/Protocols/KeychainProvider.swift
   - 目的: Keychain 操作の抽象化
   - 内容:
     ```swift
     protocol KeychainProvider: Sendable {
         func save(key: String, data: Data, service: String) async throws
         func load(key: String, service: String) async throws -> Data?
         func delete(key: String, service: String) async throws
     }
     ```
   - 注意点: Security.framework への参照なし

4. Core/HAL/Protocols/Errors.swift
   - 目的: HAL 層のエラー定義
   - 内容:
     ```swift
     enum PTYError: Error, Sendable {
         case spawnFailed(String)
         case writeFailed(String)
         case resizeFailed
         case alreadyTerminated
     }
     
     enum SerialError: Error, Sendable {
         case portNotFound(String)
         case connectionFailed(String)
         case writeFailed(String)
     }
     
     enum KeychainError: Error, Sendable {
         case saveFailed(OSStatus)
         case loadFailed(OSStatus)
         case deleteFailed(OSStatus)
         case notFound
     }
     ```
```

---

#### Task 1.4: HAL 具体実装

**目的**: macOS 向け実装(Protocol に依存)

```
【Claude Code での実装タスク】

1. Core/HAL/Darwin/DarwinPTY.swift
   - 目的: macOS での PTY 実装
   - 内容:
     - forkpty() を使用
     - AsyncStream で出力をストリーミング
     - DispatchIO または FileHandle で非同期 I/O
   - 注意点:
     - `import Darwin` のみ使用
     - PTYProvider Protocol に準拠
     - 内部実装の詳細は外部に露出しない

2. Core/HAL/Darwin/DarwinSerial.swift
   - 目的: macOS でのシリアルポート実装
   - 内容:
     - IOKit でポートスキャン
     - termios で接続設定
   - 注意点: SerialProvider Protocol に準拠

3. Core/HAL/Darwin/SystemKeychain.swift
   - 目的: macOS Keychain 実装
   - 内容:
     - Security.framework 使用
     - SecItemAdd, SecItemCopyMatching 等
   - 注意点: KeychainProvider Protocol に準拠
```

---

#### Task 1.5: HAL Mock 実装

**目的**: テスト用モック(Protocol に依存)

```
【Claude Code での実装タスク】

1. Core/HAL/Mock/MockPTY.swift
   - 目的: テスト用 PTY モック
   - 内容:
     ```swift
     final class MockPTY: PTYProvider {
         var spawnHandler: ((String, [String], [String: String]) async throws -> PTYSession)?
         var spawnCallCount = 0
         var lastSpawnArgs: (shell: String, args: [String])?
         
         func spawn(shell: String, args: [String], environment: [String: String]) async throws -> PTYSession {
             spawnCallCount += 1
             lastSpawnArgs = (shell, args)
             if let handler = spawnHandler {
                 return try await handler(shell, args, environment)
             }
             return MockPTYSession()
         }
     }
     
     final class MockPTYSession: PTYSession {
         var outputData: [Data] = []
         var writtenData: [Data] = []
         var isRunning = true
         
         var output: AsyncStream<Data> {
             AsyncStream { continuation in
                 for data in outputData {
                     continuation.yield(data)
                 }
                 continuation.finish()
             }
         }
         
         func write(_ data: Data) async throws {
             writtenData.append(data)
         }
         
         func resize(cols: Int, rows: Int) async throws {}
         
         func terminate() {
             isRunning = false
         }
     }
     ```
   - 注意点: テストで振る舞いを注入可能に

2. Core/HAL/Mock/MockSerial.swift
   - 目的: テスト用シリアルモック
   - 内容: 同様のパターンで実装

3. Core/HAL/Mock/MockKeychain.swift
   - 目的: テスト用 Keychain モック
   - 内容: インメモリで保存/読み込み
```

---

#### Task 1.6: Events 層実装

**目的**: イベント駆動通信の基盤

```
【Claude Code での実装タスク】

1. Core/Events/RavenEvent.swift
   - 目的: イベント Protocol 定義
   - 内容:
     ```swift
     protocol RavenEvent: Sendable {}
     ```

2. Core/Events/EventBus.swift
   - 目的: 型安全なイベントバス
   - 内容:
     ```swift
     import Combine
     
     final class EventBus: @unchecked Sendable {
         private let subject = PassthroughSubject<any RavenEvent, Never>()
         
         func publish<E: RavenEvent>(_ event: E) {
             subject.send(event)
         }
         
         func subscribe<E: RavenEvent>(
             _ eventType: E.Type,
             handler: @escaping (E) -> Void
         ) -> AnyCancellable {
             subject
                 .compactMap { $0 as? E }
                 .receive(on: DispatchQueue.main)
                 .sink(receiveValue: handler)
         }
     }
     ```

3. Core/Events/TerminalEvents.swift
   - 目的: ターミナル関連イベント
   - 内容:
     ```swift
     struct PTYOutputEvent: RavenEvent {
         let sessionId: UUID
         let data: Data
     }
     
     struct PTYTerminatedEvent: RavenEvent {
         let sessionId: UUID
         let exitCode: Int32?
     }
     ```

4. Core/Events/GitEvents.swift
   - 目的: Git 関連イベント
   - 内容:
     ```swift
     struct GitStatusChangedEvent: RavenEvent {
         let directory: String
         let status: GitStatus
     }
     ```

5. Core/Events/SSHEvents.swift
   - 目的: SSH 関連イベント
   - 内容:
     ```swift
     struct SSHConnectedEvent: RavenEvent {
         let connectionId: UUID
         let host: String
     }
     
     struct SSHDisconnectedEvent: RavenEvent {
         let connectionId: UUID
         let reason: String?
     }
     ```
```

---

#### Task 1.7: RavenContext 実装

**目的**: 依存性注入の中核

```
【Claude Code での実装タスク】

1. App/RavenContext.swift
   - 目的: アプリ全体の依存性管理
   - 内容:
     ```swift
     import SwiftUI
     
     @Observable
     final class RavenContext {
         // === HAL (Protocol 型で保持) ===
         let pty: any PTYProvider
         let serial: any SerialProvider
         let keychain: any KeychainProvider
         
         // === Services ===
         let eventBus: EventBus
         let settings: AppSettings
         
         // === Factory ===
         init(
             pty: any PTYProvider = DarwinPTY(),
             serial: any SerialProvider = DarwinSerial(),
             keychain: any KeychainProvider = SystemKeychain(),
             eventBus: EventBus = EventBus(),
             settings: AppSettings = .default
         ) {
             self.pty = pty
             self.serial = serial
             self.keychain = keychain
             self.eventBus = eventBus
             self.settings = settings
         }
         
         // テスト用ファクトリ
         static func forTesting(
             pty: any PTYProvider = MockPTY(),
             serial: any SerialProvider = MockSerial(),
             keychain: any KeychainProvider = MockKeychain()
         ) -> RavenContext {
             RavenContext(pty: pty, serial: serial, keychain: keychain)
         }
     }
     ```
   - 注意点:
     - 具体実装の import はこのファイルのみ
     - Protocol 型 (`any PTYProvider`) で保持
     - テスト用ファクトリメソッド提供

2. App/RavenApp.swift
   - 目的: エントリーポイント
   - 内容:
     ```swift
     import SwiftUI
     
     @main
     struct RavenApp: App {
         @State private var context = RavenContext()
         
         var body: some Scene {
             WindowGroup {
                 MainWindowView()
                     .environment(context)
             }
             .commands {
                 RavenCommands()
             }
             
             Settings {
                 SettingsView()
                     .environment(context)
             }
         }
     }
     ```
```

---

#### Task 1.8: Terminal Core 実装

**目的**: ターミナルエミュレーションのコアロジック

```
【Claude Code での実装タスク】

1. Core/Terminal/TerminalBuffer.swift
   - 目的: 画面バッファ管理
   - 内容:
     - 行/列のセル管理
     - スクロールバック履歴
     - 選択範囲管理
   - 注意点: UI 非依存、純粋なデータ構造

2. Core/Terminal/ANSIParser.swift
   - 目的: ANSI エスケープシーケンス解析
   - 内容:
     - CSI シーケンス解析
     - 色(256色/TrueColor)
     - カーソル移動
   - 注意点:
     - ステートマシンで実装
     - 外部ライブラリ不使用

3. Core/Terminal/TerminalState.swift
   - 目的: ターミナル状態管理
   - 内容:
     - カーソル位置
     - 現在の属性(色、太字等)
     - モード(挿入/置換等)

4. Core/Terminal/TerminalEmulator.swift
   - 目的: ターミナルエミュレータ本体
   - 内容:
     ```swift
     final class TerminalEmulator {
         private let buffer: TerminalBuffer
         private let parser: ANSIParser
         private var state: TerminalState
         
         private var ptySession: (any PTYSession)?
         private var outputTask: Task<Void, Never>?
         
         // Protocol 型で受け取る
         func start(using pty: any PTYProvider, shell: String, args: [String]) async throws {
             ptySession = try await pty.spawn(shell: shell, args: args, environment: [:])
             startOutputProcessing()
         }
         
         private func startOutputProcessing() {
             guard let session = ptySession else { return }
             outputTask = Task {
                 for await data in session.output {
                     await processOutput(data)
                 }
             }
         }
         
         private func processOutput(_ data: Data) async {
             // ANSI パース → バッファ更新
         }
     }
     ```
   - 注意点:
     - PTYProvider Protocol に依存(具体実装に依存しない)
     - async/await で非同期処理
```

---

#### Task 1.9: 基本 UI 実装

**目的**: SwiftUI ベースの UI 層

```
【Claude Code での実装タスク】

1. Views/MainWindow/MainWindowView.swift
   - 目的: メインウィンドウ
   - 内容:
     ```swift
     struct MainWindowView: View {
         @Environment(RavenContext.self) private var context
         
         var body: some View {
             VStack(spacing: 0) {
                 TabBarView()
                 TerminalContainerView()
                 StatusBarView()
             }
         }
     }
     ```
   - 注意点: context は Protocol 型経由でアクセス

2. Views/MainWindow/TabBarView.swift
   - 目的: タブバー

3. Views/MainWindow/StatusBarView.swift
   - 目的: ステータスバー(Git 状態等)

4. Views/Terminal/TerminalView.swift
   - 目的: ターミナル描画
   - 内容: NSViewRepresentable でラップ

5. Views/Terminal/TerminalTextView.swift
   - 目的: テキスト描画(AppKit)
   - 内容:
     - NSView サブクラス
     - Core Text で描画
     - キー入力処理
   - 注意点: 高頻度更新のため AppKit 直接使用

6. Views/Terminal/PaneContainerView.swift
   - 目的: 分割ペイン管理
```

---

#### Task 1.10: 安全機能・UX 向上

**目的**: 即追加機能の実装

```
【Claude Code での実装タスク】

1. Views/Panels/MultilinePasteConfirmView.swift
   - 目的: マルチライン貼り付け確認ダイアログ
   - 内容: 仕様書 付録C 参照

2. Features/Security/SecureKeyboardEntry.swift
   - 目的: Secure Keyboard Entry 管理
   - 内容:
     ```swift
     final class SecureKeyboardEntry {
         private var isEnabled = false
         
         func enable() {
             EnableSecureEventInput()
             isEnabled = true
         }
         
         func disable() {
             DisableSecureEventInput()
             isEnabled = false
         }
     }
     ```

3. Services/ThemeService.swift
   - 目的: OS テーマ連動
   - 内容:
     - NSApp.effectiveAppearance 監視
     - 設定に応じて自動/手動切替

4. スムーズスクロール
   - 目的: アニメーション付きスクロール
   - 場所: Views/Terminal/TerminalTextView.swift 内
   - 内容: NSAnimationContext 使用
```

---

## 3. テスト方針

### 3.1 DIP に基づくテスト容易性

```swift
// テスト例: TerminalEmulator のテスト
final class TerminalEmulatorTests: XCTestCase {
    
    func testSpawnCallsPTYProvider() async throws {
        // Arrange: Mock を注入
        let mockPTY = MockPTY()
        let emulator = TerminalEmulator()
        
        // Act
        try await emulator.start(using: mockPTY, shell: "/bin/zsh", args: [])
        
        // Assert: Mock の呼び出しを検証
        XCTAssertEqual(mockPTY.spawnCallCount, 1)
        XCTAssertEqual(mockPTY.lastSpawnArgs?.shell, "/bin/zsh")
    }
    
    func testOutputProcessing() async throws {
        // Arrange
        let mockPTY = MockPTY()
        let mockSession = MockPTYSession()
        mockSession.outputData = ["Hello".data(using: .utf8)!]
        mockPTY.spawnHandler = { _, _, _ in mockSession }
        
        let emulator = TerminalEmulator()
        
        // Act
        try await emulator.start(using: mockPTY, shell: "/bin/zsh", args: [])
        
        // Assert: バッファに反映されていることを検証
        // ...
    }
}
```

### 3.2 テストディレクトリ構造

```
RavenTests/
├── Core/
│   ├── HAL/
│   │   └── PTYProviderTests.swift    # Protocol 準拠テスト
│   ├── Terminal/
│   │   ├── ANSIParserTests.swift
│   │   └── TerminalEmulatorTests.swift
│   └── Events/
│       └── EventBusTests.swift
├── Features/
│   └── Git/
│       └── GitMonitorTests.swift
└── Integration/
    └── TerminalIntegrationTests.swift  # DarwinPTY 使用
```

---

## 4. 実装順序チェックリスト

### Phase 1 チェックリスト

```
□ Task 1.1: プロジェクト基盤
  □ Xcode プロジェクト作成
  □ ディレクトリ構造作成
  □ .gitignore 設定

□ Task 1.2: Models 層
  □ AppSettings.swift
  □ SSHConnection.swift
  □ SerialConnection.swift
  □ SessionState.swift

□ Task 1.3: HAL Protocol (※実装より先に作成)
  □ PTYProvider.swift
  □ SerialProvider.swift
  □ KeychainProvider.swift
  □ Errors.swift

□ Task 1.4: HAL 具体実装
  □ DarwinPTY.swift
  □ DarwinSerial.swift (Phase 3 でも可)
  □ SystemKeychain.swift

□ Task 1.5: HAL Mock
  □ MockPTY.swift
  □ MockSerial.swift
  □ MockKeychain.swift

□ Task 1.6: Events 層
  □ RavenEvent.swift
  □ EventBus.swift
  □ TerminalEvents.swift
  □ GitEvents.swift
  □ SSHEvents.swift

□ Task 1.7: Context
  □ RavenContext.swift
  □ RavenApp.swift

□ Task 1.8: Terminal Core
  □ TerminalBuffer.swift
  □ ANSIParser.swift
  □ TerminalState.swift
  □ TerminalEmulator.swift

□ Task 1.9: 基本 UI
  □ MainWindowView.swift
  □ TabBarView.swift
  □ StatusBarView.swift
  □ TerminalView.swift
  □ TerminalTextView.swift
  □ PaneContainerView.swift

□ Task 1.10: 安全機能・UX
  □ MultilinePasteConfirmView.swift
  □ SecureKeyboardEntry.swift
  □ ThemeService.swift
  □ スムーズスクロール実装
```

---

## 5. 注意事項

### 5.1 DIP 違反のチェックポイント

以下のパターンが出現したら DIP 違反の可能性:

```swift
// ❌ 具体クラスを直接インスタンス化
let pty = DarwinPTY()  // Views/Features 層で書いてはいけない

// ❌ 具体クラスの型を引数に
func setup(pty: DarwinPTY)  // Protocol 型にすべき

// ❌ Darwin/IOKit の import が Core/Features 層にある
import Darwin  // HAL/Darwin/ 以外で使用禁止
import IOKit   // HAL/Darwin/ 以外で使用禁止
```

### 5.2 レビューポイント

- [ ] Protocol は実装の詳細を露出していないか
- [ ] 具体実装への依存は App 層のみか
- [ ] Mock が全 Protocol に対応しているか
- [ ] テストが Mock を使用しているか

---

**以上**
