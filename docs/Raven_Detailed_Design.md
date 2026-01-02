# 🦅 Raven - 詳細設計書

## v0.1.0

**作成日**: 2025-12-25
**対象**: 基本仕様書 v0.4.0

---

## 1. テーマシステム設計

### 1.1 概要

Raven のテーマシステムは、外部 JSON ファイルによるカラー定義と、色覚多様性(色盲・色弱)に配慮したアクセシビリティ機能を提供する。

### 1.2 テーマファイル仕様

#### 1.2.1 ファイル形式

**フォーマット**: JSON
**エンコーディング**: UTF-8
**拡張子**: `.json`

#### 1.2.2 ファイル配置

```
# システムテーマ(読み取り専用)
/Applications/Raven.app/Contents/Resources/themes/
├── default-dark.json
├── default-light.json
├── colorblind-dark.json
└── colorblind-light.json

# ユーザーテーマ(読み書き可能)
~/.config/raven/themes/
├── my-custom-theme.json
└── *.json
```

**読み込み優先順位**:
1. ユーザーテーマ (`~/.config/raven/themes/`)
2. システムテーマ (`Resources/themes/`)

同名ファイルがある場合、ユーザーテーマが優先される。

#### 1.2.3 テーマファイルスキーマ

```json
{
  "$schema": "https://raven-terminal.app/schemas/theme-v1.json",
  "meta": {
    "name": "Raven Default Dark",
    "author": "Raven Team",
    "version": "1.0.0",
    "description": "Default dark theme for Raven terminal",
    "license": "MIT"
  },
  "accessibility": {
    "colorblindFriendly": false,
    "type": null,
    "highContrast": false,
    "wcagLevel": "AA"
  },
  "appearance": "dark",
  "colors": {
    "terminal": {
      "background": "#1E1E1E",
      "foreground": "#D4D4D4",
      "cursor": "#FFFFFF",
      "cursorText": "#1E1E1E",
      "selection": "#264F78",
      "selectionText": null
    },
    "ansi": {
      "black": "#000000",
      "red": "#CD3131",
      "green": "#0DBC79",
      "yellow": "#E5E510",
      "blue": "#2472C8",
      "magenta": "#BC3FBC",
      "cyan": "#11A8CD",
      "white": "#E5E5E5",
      "brightBlack": "#666666",
      "brightRed": "#F14C4C",
      "brightGreen": "#23D18B",
      "brightYellow": "#F5F543",
      "brightBlue": "#3B8EEA",
      "brightMagenta": "#D670D6",
      "brightCyan": "#29B8DB",
      "brightWhite": "#FFFFFF"
    },
    "ui": {
      "tabBar": {
        "background": "#252526",
        "foreground": "#CCCCCC",
        "activeBackground": "#1E1E1E",
        "activeForeground": "#FFFFFF",
        "border": "#444444"
      },
      "statusBar": {
        "background": "#007ACC",
        "foreground": "#FFFFFF"
      },
      "pane": {
        "divider": "#444444",
        "dividerActive": "#007ACC"
      },
      "scrollbar": {
        "track": "#1E1E1E",
        "thumb": "#5A5A5A",
        "thumbHover": "#7A7A7A"
      }
    },
    "semantic": {
      "success": {
        "background": "#89D185",
        "foreground": "#1E1E1E",
        "icon": "checkmark.circle.fill"
      },
      "warning": {
        "background": "#CCA700",
        "foreground": "#1E1E1E",
        "icon": "exclamationmark.triangle.fill"
      },
      "error": {
        "background": "#F44747",
        "foreground": "#FFFFFF",
        "icon": "xmark.circle.fill"
      },
      "info": {
        "background": "#56B6C2",
        "foreground": "#1E1E1E",
        "icon": "info.circle.fill"
      }
    },
    "git": {
      "ahead": {
        "color": "#89D185",
        "icon": "arrow.up",
        "text": "↑"
      },
      "behind": {
        "color": "#CCA700",
        "icon": "arrow.down",
        "text": "↓"
      },
      "conflict": {
        "color": "#F44747",
        "icon": "exclamationmark.triangle",
        "text": "!"
      },
      "modified": {
        "color": "#E5C07B",
        "icon": "pencil",
        "text": "M"
      },
      "untracked": {
        "color": "#98C379",
        "icon": "plus",
        "text": "U"
      },
      "stash": {
        "color": "#C678DD",
        "icon": "tray",
        "text": "S"
      },
      "staged": {
        "color": "#89D185",
        "icon": "checkmark",
        "text": "+"
      }
    }
  }
}
```

#### 1.2.4 色覚多様性対応テーマ

```json
{
  "meta": {
    "name": "Raven Colorblind Dark",
    "author": "Raven Team",
    "version": "1.0.0",
    "description": "Color vision deficiency friendly dark theme"
  },
  "accessibility": {
    "colorblindFriendly": true,
    "type": "deuteranopia-protanopia",
    "highContrast": false,
    "wcagLevel": "AA"
  },
  "appearance": "dark",
  "colors": {
    "terminal": {
      "background": "#1E1E1E",
      "foreground": "#D4D4D4",
      "cursor": "#FFFFFF",
      "cursorText": "#1E1E1E",
      "selection": "#264F78",
      "selectionText": null
    },
    "ansi": {
      "black": "#000000",
      "red": "#C678DD",
      "green": "#56B6C2",
      "yellow": "#D19A66",
      "blue": "#2472C8",
      "magenta": "#BC3FBC",
      "cyan": "#11A8CD",
      "white": "#E5E5E5",
      "brightBlack": "#666666",
      "brightRed": "#E06C75",
      "brightGreen": "#61AFEF",
      "brightYellow": "#E5C07B",
      "brightBlue": "#3B8EEA",
      "brightMagenta": "#D670D6",
      "brightCyan": "#29B8DB",
      "brightWhite": "#FFFFFF"
    },
    "semantic": {
      "success": {
        "background": "#56B6C2",
        "foreground": "#1E1E1E",
        "icon": "checkmark.circle.fill"
      },
      "warning": {
        "background": "#D19A66",
        "foreground": "#1E1E1E",
        "icon": "exclamationmark.triangle.fill"
      },
      "error": {
        "background": "#C678DD",
        "foreground": "#FFFFFF",
        "icon": "xmark.circle.fill"
      },
      "info": {
        "background": "#61AFEF",
        "foreground": "#1E1E1E",
        "icon": "info.circle.fill"
      }
    },
    "git": {
      "ahead": {
        "color": "#56B6C2",
        "icon": "arrow.up",
        "text": "↑"
      },
      "behind": {
        "color": "#D19A66",
        "icon": "arrow.down",
        "text": "↓"
      },
      "conflict": {
        "color": "#C678DD",
        "icon": "exclamationmark.triangle",
        "text": "!"
      },
      "modified": {
        "color": "#D19A66",
        "icon": "pencil",
        "text": "M"
      },
      "untracked": {
        "color": "#61AFEF",
        "icon": "plus",
        "text": "U"
      },
      "stash": {
        "color": "#C678DD",
        "icon": "tray",
        "text": "S"
      },
      "staged": {
        "color": "#56B6C2",
        "icon": "checkmark",
        "text": "+"
      }
    }
  }
}
```

### 1.3 テーマシステムアーキテクチャ

#### 1.3.1 クラス図

```
┌─────────────────────────────────────────────────────────────┐
│                      ThemeManager                           │
├─────────────────────────────────────────────────────────────┤
│ - currentTheme: Theme                                       │
│ - availableThemes: [ThemeMetadata]                          │
│ - themeDirectories: [URL]                                   │
├─────────────────────────────────────────────────────────────┤
│ + loadTheme(name: String) -> Theme                          │
│ + listAvailableThemes() -> [ThemeMetadata]                  │
│ + reloadThemes()                                            │
│ + applyTheme(_ theme: Theme)                                │
│ + exportTheme(_ theme: Theme, to: URL)                      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                         Theme                               │
├─────────────────────────────────────────────────────────────┤
│ + meta: ThemeMeta                                           │
│ + accessibility: ThemeAccessibility                         │
│ + appearance: Appearance                                    │
│ + colors: ThemeColors                                       │
├─────────────────────────────────────────────────────────────┤
│ + color(for: SemanticColor) -> Color                        │
│ + gitStatus(for: GitStatusType) -> GitStatusStyle           │
│ + ansiColor(_ index: Int) -> Color                          │
└─────────────────────────────────────────────────────────────┘
                            │
            ┌───────────────┼───────────────┐
            ▼               ▼               ▼
┌───────────────────┐ ┌───────────────┐ ┌───────────────────┐
│   ThemeColors     │ │  ThemeMeta    │ │ThemeAccessibility │
├───────────────────┤ ├───────────────┤ ├───────────────────┤
│ terminal: ...     │ │ name: String  │ │ colorblindFriendly│
│ ansi: ...         │ │ author: String│ │ type: CVDType?    │
│ ui: ...           │ │ version: ...  │ │ highContrast: Bool│
│ semantic: ...     │ │ description   │ │ wcagLevel: String │
│ git: ...          │ │ license: ...  │ └───────────────────┘
└───────────────────┘ └───────────────┘
```

#### 1.3.2 Swift 実装

```swift
// MARK: - Theme Models

struct Theme: Codable {
    let meta: ThemeMeta
    let accessibility: ThemeAccessibility
    let appearance: Appearance
    let colors: ThemeColors
}

struct ThemeMeta: Codable {
    let name: String
    let author: String
    let version: String
    let description: String?
    let license: String?
}

struct ThemeAccessibility: Codable {
    let colorblindFriendly: Bool
    let type: ColorVisionDeficiencyType?
    let highContrast: Bool
    let wcagLevel: String?
}

enum ColorVisionDeficiencyType: String, Codable {
    case deuteranopia = "deuteranopia"           // 緑色盲
    case protanopia = "protanopia"               // 赤色盲
    case tritanopia = "tritanopia"               // 青色盲
    case deuteranomalyProtanomaly = "deuteranopia-protanopia"  // 赤緑色覚異常
}

enum Appearance: String, Codable {
    case dark
    case light
}

struct ThemeColors: Codable {
    let terminal: TerminalColors
    let ansi: ANSIColors
    let ui: UIColors
    let semantic: SemanticColors
    let git: GitColors
}

struct TerminalColors: Codable {
    let background: String
    let foreground: String
    let cursor: String
    let cursorText: String?
    let selection: String
    let selectionText: String?
}

struct ANSIColors: Codable {
    let black: String
    let red: String
    let green: String
    let yellow: String
    let blue: String
    let magenta: String
    let cyan: String
    let white: String
    let brightBlack: String
    let brightRed: String
    let brightGreen: String
    let brightYellow: String
    let brightBlue: String
    let brightMagenta: String
    let brightCyan: String
    let brightWhite: String
    
    func color(at index: Int) -> String {
        switch index {
        case 0: return black
        case 1: return red
        case 2: return green
        case 3: return yellow
        case 4: return blue
        case 5: return magenta
        case 6: return cyan
        case 7: return white
        case 8: return brightBlack
        case 9: return brightRed
        case 10: return brightGreen
        case 11: return brightYellow
        case 12: return brightBlue
        case 13: return brightMagenta
        case 14: return brightCyan
        case 15: return brightWhite
        default: return white
        }
    }
}

struct SemanticColors: Codable {
    let success: SemanticColorDefinition
    let warning: SemanticColorDefinition
    let error: SemanticColorDefinition
    let info: SemanticColorDefinition
}

struct SemanticColorDefinition: Codable {
    let background: String
    let foreground: String
    let icon: String  // SF Symbols name
}

struct GitColors: Codable {
    let ahead: GitStatusStyle
    let behind: GitStatusStyle
    let conflict: GitStatusStyle
    let modified: GitStatusStyle
    let untracked: GitStatusStyle
    let stash: GitStatusStyle
    let staged: GitStatusStyle
}

struct GitStatusStyle: Codable {
    let color: String
    let icon: String   // SF Symbols name
    let text: String   // テキスト表現(↑, ↓, ! など)
}
```

#### 1.3.3 ThemeManager 実装

```swift
import SwiftUI
import Combine

@Observable
final class ThemeManager {
    
    // MARK: - Properties
    
    private(set) var currentTheme: Theme
    private(set) var availableThemes: [ThemeMeta] = []
    
    private let userThemeDirectory: URL
    private let systemThemeDirectory: URL
    
    // MARK: - Initialization
    
    init() {
        // ディレクトリ設定
        userThemeDirectory = FileManager.default
            .homeDirectoryForCurrentUser
            .appendingPathComponent(".config/raven/themes")
        
        systemThemeDirectory = Bundle.main
            .resourceURL?
            .appendingPathComponent("themes") ?? URL(fileURLWithPath: "/")
        
        // デフォルトテーマ読み込み
        currentTheme = Self.loadDefaultTheme()
        
        // テーマ一覧更新
        reloadAvailableThemes()
    }
    
    // MARK: - Public Methods
    
    func loadTheme(named name: String) throws {
        // ユーザーテーマを優先検索
        let userThemeURL = userThemeDirectory.appendingPathComponent("\(name).json")
        let systemThemeURL = systemThemeDirectory.appendingPathComponent("\(name).json")
        
        let themeURL: URL
        if FileManager.default.fileExists(atPath: userThemeURL.path) {
            themeURL = userThemeURL
        } else if FileManager.default.fileExists(atPath: systemThemeURL.path) {
            themeURL = systemThemeURL
        } else {
            throw ThemeError.themeNotFound(name)
        }
        
        let data = try Data(contentsOf: themeURL)
        let theme = try JSONDecoder().decode(Theme.self, from: data)
        currentTheme = theme
    }
    
    func reloadAvailableThemes() {
        var themes: [ThemeMeta] = []
        
        // システムテーマ
        themes.append(contentsOf: scanThemes(in: systemThemeDirectory))
        
        // ユーザーテーマ(重複は上書き)
        let userThemes = scanThemes(in: userThemeDirectory)
        for userTheme in userThemes {
            if let index = themes.firstIndex(where: { $0.name == userTheme.name }) {
                themes[index] = userTheme
            } else {
                themes.append(userTheme)
            }
        }
        
        availableThemes = themes.sorted { $0.name < $1.name }
    }
    
    func exportTheme(_ theme: Theme, to url: URL) throws {
        let encoder = JSONEncoder()
        encoder.outputFormatting = [.prettyPrinted, .sortedKeys]
        let data = try encoder.encode(theme)
        try data.write(to: url)
    }
    
    // MARK: - Private Methods
    
    private func scanThemes(in directory: URL) -> [ThemeMeta] {
        guard let files = try? FileManager.default.contentsOfDirectory(
            at: directory,
            includingPropertiesForKeys: nil
        ) else {
            return []
        }
        
        return files
            .filter { $0.pathExtension == "json" }
            .compactMap { url -> ThemeMeta? in
                guard let data = try? Data(contentsOf: url),
                      let theme = try? JSONDecoder().decode(Theme.self, from: data) else {
                    return nil
                }
                return theme.meta
            }
    }
    
    private static func loadDefaultTheme() -> Theme {
        // バンドル内のデフォルトテーマを読み込み
        guard let url = Bundle.main.url(forResource: "default-dark", withExtension: "json"),
              let data = try? Data(contentsOf: url),
              let theme = try? JSONDecoder().decode(Theme.self, from: data) else {
            fatalError("Default theme not found in bundle")
        }
        return theme
    }
}

enum ThemeError: Error, LocalizedError {
    case themeNotFound(String)
    case invalidThemeFile(String)
    case parseError(String)
    
    var errorDescription: String? {
        switch self {
        case .themeNotFound(let name):
            return "Theme '\(name)' not found"
        case .invalidThemeFile(let path):
            return "Invalid theme file at '\(path)'"
        case .parseError(let message):
            return "Failed to parse theme: \(message)"
        }
    }
}
```

### 1.4 SwiftUI カラー変換

```swift
extension String {
    /// HEX 文字列を SwiftUI Color に変換
    var asColor: Color {
        let hex = trimmingCharacters(in: CharacterSet(charactersIn: "#"))
        guard hex.count == 6,
              let int = UInt64(hex, radix: 16) else {
            return .clear
        }
        
        let r = Double((int >> 16) & 0xFF) / 255.0
        let g = Double((int >> 8) & 0xFF) / 255.0
        let b = Double(int & 0xFF) / 255.0
        
        return Color(red: r, green: g, blue: b)
    }
}

extension Theme {
    /// セマンティックカラーを取得
    func semanticColor(_ type: SemanticColorType) -> Color {
        switch type {
        case .success: return colors.semantic.success.background.asColor
        case .warning: return colors.semantic.warning.background.asColor
        case .error: return colors.semantic.error.background.asColor
        case .info: return colors.semantic.info.background.asColor
        }
    }
    
    /// Git ステータスのスタイルを取得
    func gitStyle(_ status: GitStatusType) -> GitStatusStyle {
        switch status {
        case .ahead: return colors.git.ahead
        case .behind: return colors.git.behind
        case .conflict: return colors.git.conflict
        case .modified: return colors.git.modified
        case .untracked: return colors.git.untracked
        case .stash: return colors.git.stash
        case .staged: return colors.git.staged
        }
    }
}

enum SemanticColorType {
    case success, warning, error, info
}

enum GitStatusType {
    case ahead, behind, conflict, modified, untracked, stash, staged
}
```

---

## 2. アクセシビリティ設計

### 2.1 色覚多様性対応

#### 2.1.1 対応する色覚特性

| タイプ | 日本語名 | 特徴 | 発生率 |
|--------|---------|------|--------|
| Deuteranopia | 2型2色覚(緑色盲) | 緑を感じにくい | 男性 1% |
| Protanopia | 1型2色覚(赤色盲) | 赤を感じにくい | 男性 1% |
| Deuteranomaly | 2型3色覚(緑色弱) | 緑の感度が低い | 男性 5% |
| Protanomaly | 1型3色覚(赤色弱) | 赤の感度が低い | 男性 1% |
| Tritanopia | 3型2色覚(青色盲) | 青を感じにくい | 0.001% |

**設計方針**: 最も発生率の高い赤緑色覚異常(Deuteranopia/Protanopia)を優先対応

#### 2.1.2 色の選定基準

**避けるべき組み合わせ**:
- 赤 vs 緑(最重要)
- 赤 vs 茶
- 緑 vs 茶
- 青 vs 紫
- ピンク vs グレー

**推奨される組み合わせ**:
- 青 vs オレンジ
- 青 vs 黄
- 紫/マゼンタ vs 緑/シアン
- 白 vs 黒(高コントラスト)

#### 2.1.3 色覚対応カラーパレット

```
デフォルト          色覚対応
─────────────────────────────────
成功  #89D185 緑  → #56B6C2 シアン
警告  #CCA700 黄  → #D19A66 オレンジ
エラー #F44747 赤  → #C678DD マゼンタ
情報  #56B6C2 青  → #61AFEF 明るい青
```

### 2.2 アイコンによる補助表示

#### 2.2.1 設計原則

> **色だけに依存しない情報伝達**
> 
> すべてのステータス表示は、色 + アイコン + テキストの3要素で構成し、
> いずれか1つでも意味が伝わるようにする。

#### 2.2.2 Git ステータスアイコン

| 状態 | SF Symbol | Unicode | 表示例 |
|------|-----------|---------|--------|
| Ahead | `arrow.up` | ↑ | `↑2` |
| Behind | `arrow.down` | ↓ | `↓1` |
| Conflict | `exclamationmark.triangle` | ! | `!3` |
| Modified | `pencil` | M | `M:5` |
| Untracked | `plus` | U | `U:2` |
| Staged | `checkmark` | + | `+:3` |
| Stash | `tray` | S | `S:1` |

#### 2.2.3 SwiftUI 実装

```swift
struct GitStatusBadge: View {
    let status: GitStatusType
    let count: Int
    let showIcon: Bool
    
    @Environment(ThemeManager.self) private var themeManager
    
    var body: some View {
        let style = themeManager.currentTheme.gitStyle(status)
        
        HStack(spacing: 2) {
            if showIcon {
                Image(systemName: style.icon)
                    .font(.system(size: 10, weight: .semibold))
            }
            Text("\(style.text)\(count)")
                .font(.system(size: 11, weight: .medium, design: .monospaced))
        }
        .foregroundColor(style.color.asColor)
        .accessibilityLabel(accessibilityLabel)
    }
    
    private var accessibilityLabel: String {
        switch status {
        case .ahead: return "\(count) commits ahead"
        case .behind: return "\(count) commits behind"
        case .conflict: return "\(count) conflicts"
        case .modified: return "\(count) files modified"
        case .untracked: return "\(count) untracked files"
        case .staged: return "\(count) files staged"
        case .stash: return "\(count) stashes"
        }
    }
}
```

### 2.3 VoiceOver 対応

#### 2.3.1 アクセシビリティラベル

すべての UI 要素に適切な `accessibilityLabel` を設定:

```swift
// タブ
.accessibilityLabel("Tab: \(title), \(isActive ? "active" : "inactive")")

// Git ステータス
.accessibilityLabel("Git status: branch \(branch), \(ahead) ahead, \(behind) behind")

// ターミナル出力
.accessibilityElement(children: .contain)
.accessibilityLabel("Terminal output")
```

#### 2.3.2 キーボードナビゲーション

| 操作 | キー |
|------|------|
| タブ間移動 | Cmd+Shift+[ / ] |
| ペイン間移動 | Cmd+Option+矢印 |
| 設定画面 | Cmd+, |
| クイック接続 | Cmd+K |

---

## 3. コンポーネント詳細設計

### 3.1 TerminalBuffer

#### 3.1.1 責務

- 画面バッファ(セル行列)の管理
- スクロールバック履歴の保持
- 選択範囲の管理
- カーソル位置の管理

#### 3.1.2 データ構造

```swift
struct TerminalCell {
    var character: Character
    var attribute: CellAttribute
}

struct CellAttribute {
    var foregroundColor: UInt8      // ANSI color index or 24-bit
    var backgroundColor: UInt8
    var foreground24bit: (UInt8, UInt8, UInt8)?
    var background24bit: (UInt8, UInt8, UInt8)?
    var bold: Bool
    var italic: Bool
    var underline: Bool
    var strikethrough: Bool
    var inverse: Bool
    var blink: Bool
    var dim: Bool
    var hidden: Bool
}

final class TerminalBuffer {
    // 画面サイズ
    private(set) var cols: Int
    private(set) var rows: Int
    
    // アクティブバッファ(画面表示領域)
    private var screenBuffer: [[TerminalCell]]
    
    // スクロールバック(履歴)
    private var scrollbackBuffer: [[TerminalCell]]
    private let maxScrollbackLines: Int
    
    // カーソル
    private(set) var cursorX: Int = 0
    private(set) var cursorY: Int = 0
    
    // 選択
    private(set) var selection: SelectionRange?
    
    // スクロール位置
    private(set) var scrollOffset: Int = 0
}

struct SelectionRange {
    var start: (x: Int, y: Int)
    var end: (x: Int, y: Int)
}
```

### 3.2 ANSIParser

#### 3.2.1 ステートマシン

```
┌─────────┐  文字   ┌─────────┐
│  Ground │───────▶│  Print  │
└─────────┘        └─────────┘
     │
     │ ESC
     ▼
┌─────────┐   [    ┌─────────┐  数字/;  ┌─────────┐
│ Escape  │───────▶│  CSI    │─────────▶│CSI Param│
└─────────┘        └─────────┘          └─────────┘
     │                                       │
     │ ]                                     │ 英字
     ▼                                       ▼
┌─────────┐  数字  ┌─────────┐         ┌─────────┐
│  OSC    │───────▶│OSC Param│         │CSI Final│
└─────────┘        └─────────┘         └─────────┘
```

#### 3.2.2 実装

```swift
enum ParserState {
    case ground
    case escape
    case escapeIntermediate
    case csiEntry
    case csiParam
    case csiIntermediate
    case csiIgnore
    case oscString
    case dcsEntry
    case dcsParam
    case dcsPassthrough
}

final class ANSIParser {
    private var state: ParserState = .ground
    private var params: [Int] = []
    private var intermediates: [UInt8] = []
    private var oscString: String = ""
    
    weak var delegate: ANSIParserDelegate?
    
    func parse(_ data: Data) {
        for byte in data {
            process(byte)
        }
    }
    
    private func process(_ byte: UInt8) {
        switch state {
        case .ground:
            handleGround(byte)
        case .escape:
            handleEscape(byte)
        case .csiEntry:
            handleCSIEntry(byte)
        case .csiParam:
            handleCSIParam(byte)
        case .oscString:
            handleOSCString(byte)
        // ... その他の状態
        }
    }
}

protocol ANSIParserDelegate: AnyObject {
    func parser(_ parser: ANSIParser, didReceiveCharacter char: Character)
    func parser(_ parser: ANSIParser, didReceiveCSI params: [Int], intermediate: [UInt8], final: UInt8)
    func parser(_ parser: ANSIParser, didReceiveOSC command: Int, data: String)
    func parser(_ parser: ANSIParser, didReceiveSGR params: [Int])
}
```

### 3.3 EventBus

#### 3.3.1 イベント定義

```swift
// ターミナルイベント
struct TerminalOutputEvent: RavenEvent {
    let sessionId: UUID
    let data: Data
}

struct TerminalResizeEvent: RavenEvent {
    let sessionId: UUID
    let cols: Int
    let rows: Int
}

// Git イベント
struct GitStatusChangedEvent: RavenEvent {
    let directory: String
    let branch: String
    let ahead: Int
    let behind: Int
    let modified: Int
    let untracked: Int
    let conflicts: Int
    let stashes: Int
}

// テーマイベント
struct ThemeChangedEvent: RavenEvent {
    let themeName: String
    let appearance: Appearance
}

// 設定イベント
struct SettingsChangedEvent: RavenEvent {
    let key: String
    let oldValue: Any?
    let newValue: Any?
}
```

---

## 4. 改訂履歴

| バージョン | 日付 | 内容 |
|-----------|------|------|
| 0.1.0 | 2025-12-25 | 初版作成。テーマシステム、アクセシビリティ、コンポーネント詳細設計 |

---

**以上**
