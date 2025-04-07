# メインHUD実装手順

## 1. メインHUDウィジェットの作成

### 基本設定
1. コンテンツブラウザで `/Game/UI/` フォルダに移動します
2. 右クリック → ユーザーインターフェース → ウィジェットブループリント を選択
3. 名前を `WBP_MainHUD` に設定
4. 新しく作成したウィジェットを開き、「親クラス」を `UASMainHUDWidget` に設定
5. デザイナータブで「パレット」から必要なウィジェットをドラッグ＆ドロップします

### レイアウト構成
メインHUDは以下の4つの主要セクションで構成します：

## 2. 各セクションの詳細実装

### 2-1. プレイヤー情報セクション（画面左下）

1. **キャンバスパネルに「アンカー」を設定**
   - サイズを「左下」に設定
   - 位置: X=50, Y=-150（画面下から150ピクセル上）

2. **垂直ボックス（Vertical Box）の追加**
   - サイズ: 300x150
   - パディング: 上下左右5ピクセル
   
3. **プレイヤーアイコンと名前の水平ボックス（Horizontal Box）**
   - 「垂直ボックス」内に配置
   - サイズフィル: 0.4（垂直ボックスの高さの40%）
   - 内部コンポーネント:
     - イメージ（キャラクターアイコン）
       - サイズ: 64x64
       - ブラシ: `/Game/UI/Textures/T_PlayerIcon`（別途作成するアイコン画像）
     - テキストブロック（プレイヤー名）
       - フォント: Roboto（サイズ18）
       - 色: 白 (R:1, G:1, B:1, A:1)
       - テキスト: バインド → GetPlayerName関数

4. **HPバー（Progress Bar）**
   - 「垂直ボックス」内に配置
   - サイズフィル: 0.3（垂直ボックスの高さの30%）
   - 前景色: 赤 (R:1, G:0.2, B:0.2, A:1)
   - 背景色: 半透明黒 (R:0, G:0, B:0, A:0.5)
   - パーセント: バインド → GetPlayerHealthPercent関数
   - ツールチップテキスト: "プレイヤーHP"
   
5. **HPテキスト用水平ボックス**
   - 「垂直ボックス」内に配置（HPバーの下）
   - サイズフィル: 0.3（垂直ボックスの高さの30%）
   - 水平配置: 中央
   - 内部コンポーネント:
     - テキストブロック（現在HP / 最大HP）
       - フォント: Roboto（サイズ14）
       - 色: 白 (R:1, G:1, B:1, A:1)
       - テキスト: バインド → GetPlayerHealthText関数（"100/100"の形式）

### 2-2. マナ情報セクション（画面左上）

1. **キャンバスパネルに「アンカー」を設定**
   - サイズを「左上」に設定
   - 位置: X=50, Y=50

2. **水平ボックス（Horizontal Box）の追加**
   - サイズ: 300x80
   - パディング: 上下左右5ピクセル
   
3. **マナアイコンの設定**
   - 「水平ボックス」内に配置
   - イメージ（マナアイコン）
     - サイズ: 64x64
     - ブラシ: `/Game/UI/Textures/T_ManaIcon`（別途作成するアイコン画像）
   
4. **マナ情報用垂直ボックス**
   - 「水平ボックス」内に配置（マナアイコンの右）
   - サイズフィル: 1.0（残りのスペースを全て使用）
   - パディング: 左10ピクセル（アイコンとテキスト間の余白）
   - 内部コンポーネント:
     - テキストブロック（現在のマナ値）
       - フォント: Roboto（サイズ24、Bold）
       - 色: 青 (R:0.2, G:0.5, B:1.0, A:1)
       - テキスト: バインド → GetCurrentMana関数
     - テキストブロック（マナ増加率）
       - フォント: Roboto（サイズ16）
       - 色: 水色 (R:0.5, G:0.7, B:1.0, A:1)
       - テキスト: バインド → GetManaIncreaseRateText関数（"+XX/10秒"の形式）

### 2-3. ゲーム情報セクション（画面上部中央）

1. **キャンバスパネルに「アンカー」を設定**
   - サイズを「上部中央」に設定
   - 位置: X=0, Y=50

2. **垂直ボックス（Vertical Box）の追加**
   - サイズ: 400x120
   - 水平配置: 中央
   
3. **残り時間の設定**
   - 「垂直ボックス」内に配置
   - テキストブロック（残り時間）
     - フォント: Roboto（サイズ32、Bold）
     - 色: 白 (R:1, G:1, B:1, A:1)
     - シャドウオフセット: X=2, Y=2
     - シャドウ色: 黒 (R:0, G:0, B:0, A:0.7)
     - 配置: 中央
     - テキスト: バインド → GetRemainingTimeText関数（"3:45"の形式）
   
4. **ゴースト占領情報用水平ボックス**
   - 「垂直ボックス」内に配置（残り時間の下）
   - パディング: 上10ピクセル（時間との間隔）
   - 内部コンポーネント:
     - 水平ボックス（味方ゴースト情報）
       - サイズフィル: 0.33（水平ボックスの幅の1/3）
       - イメージ（味方ゴーストアイコン）
         - サイズ: 32x32
         - ブラシ: `/Game/UI/Textures/T_FriendlyGhost`
       - テキストブロック（味方ゴースト数）
         - フォント: Roboto（サイズ18）
         - 色: 青 (R:0.2, G:0.5, B:1.0, A:1)
         - テキスト: バインド → GetFriendlyGhostCount関数
     - 水平ボックス（中立ゴースト情報）
       - サイズフィル: 0.33（水平ボックスの幅の1/3）
       - イメージ（中立ゴーストアイコン）
         - サイズ: 32x32
         - ブラシ: `/Game/UI/Textures/T_NeutralGhost`
       - テキストブロック（中立ゴースト数）
         - フォント: Roboto（サイズ18）
         - 色: 灰色 (R:0.7, G:0.7, B:0.7, A:1)
         - テキスト: バインド → GetNeutralGhostCount関数
     - 水平ボックス（敵ゴースト情報）
       - サイズフィル: 0.33（水平ボックスの幅の1/3）
       - イメージ（敵ゴーストアイコン）
         - サイズ: 32x32
         - ブラシ: `/Game/UI/Textures/T_EnemyGhost`
       - テキストブロック（敵ゴースト数）
         - フォント: Roboto（サイズ18）
         - 色: 赤 (R:1, G:0.2, B:0.2, A:1)
         - テキスト: バインド → GetEnemyGhostCount関数

### 2-4. マスターゴーストHP情報（画面上部全体）

1. **キャンバスパネルに「アンカー」を設定**
   - サイズを「上部中央」に設定
   - 位置: X=0, Y=150（残り時間の下）

2. **水平ボックス（Horizontal Box）の追加**
   - サイズ: 800x60
   - 水平配置: 中央
   
3. **味方マスターゴーストHPバー**
   - 「水平ボックス」内に配置（左側）
   - プログレスバー
     - サイズ: 350x40
     - 塗りつぶし方向: 右から左
     - 前景色: 青 (R:0.2, G:0.5, B:1.0, A:1)
     - 背景色: 半透明黒 (R:0, G:0, B:0, A:0.5)
     - パーセント: バインド → GetFriendlyMasterGhostHealthPercent関数
   
4. **中央区切り**
   - 「水平ボックス」内に配置（中央）
   - ボーダー
     - サイズ: 100x40
     - 境界線の太さ: 2
     - 境界線の色: 灰色 (R:0.5, G:0.5, B:0.5, A:1)
     - 内部コンポーネント:
       - テキストブロック（VS表示）
         - フォント: Roboto（サイズ24、Bold）
         - 色: 黄色 (R:1, G:1, B:0.2, A:1)
         - 配置: 中央
         - テキスト: "VS"
   
5. **敵マスターゴーストHPバー**
   - 「水平ボックス」内に配置（右側）
   - プログレスバー
     - サイズ: 350x40
     - 塗りつぶし方向: 左から右
     - 前景色: 赤 (R:1, G:0.2, B:0.2, A:1)
     - 背景色: 半透明黒 (R:0, G:0, B:0, A:0.5)
     - パーセント: バインド → GetEnemyMasterGhostHealthPercent関数

## 3. ブループリントの実装

「グラフ」タブに移動して、以下の実装を行います。

### 3-1. イベントグラフの基本設定

```
Event Construct
  → Set Layout Scale (1.0)
  → Set Visibility (Visible)
```

### 3-2. データバインド関数の実装

必要な変数を作成します：
- PlayerHealthPercent (Float)
- FriendlyMasterGhostHealthPercent (Float)
- EnemyMasterGhostHealthPercent (Float)
- CurrentManaValue (Float)
- ManaIncreaseRateValue (Float)
- RemainingTimeValue (Float)
- FriendlyGhostCountValue (Integer)
- EnemyGhostCountValue (Integer)
- NeutralGhostCountValue (Integer)

### 3-3. 各バインド関数の実装

```
// プレイヤーHP関連
Function GetPlayerHealthPercent() → Float
  Return PlayerHealthPercent

Function GetPlayerHealthText() → Text
  Local Text HealthText
  Set HealthText = Format("{0}/{1}", PlayerHealthPercent * 100, 100)
  Return HealthText

// マナ関連
Function GetCurrentMana() → Text
  Return FText::AsNumber(CurrentManaValue)

Function GetManaIncreaseRateText() → Text
  Return FText::Format("+{0}/10秒", ManaIncreaseRateValue)

// 時間関連
Function GetRemainingTimeText() → Text
  Local Integer Minutes = Floor(RemainingTimeValue / 60)
  Local Integer Seconds = Floor(RemainingTimeValue % 60)
  Return FText::Format("{0}:{1:02d}", Minutes, Seconds)

// ゴースト数関連
Function GetFriendlyGhostCount() → Text
  Return FText::AsNumber(FriendlyGhostCountValue)

Function GetEnemyGhostCount() → Text
  Return FText::AsNumber(EnemyGhostCountValue)

Function GetNeutralGhostCount() → Text
  Return FText::AsNumber(NeutralGhostCountValue)

// マスターゴーストHP関連
Function GetFriendlyMasterGhostHealthPercent() → Float
  Return FriendlyMasterGhostHealthPercent

Function GetEnemyMasterGhostHealthPercent() → Float
  Return EnemyMasterGhostHealthPercent
```

### 3-4. 情報更新イベント関数の実装

```
// BP用イベント関数の実装
Event UpdateHealthInfo(Float PlayerHealth, Float PlayerMaxHealth, Float FriendlyMasterGhostHealth, Float EnemyMasterGhostHealth)
  Set PlayerHealthPercent = PlayerHealth / PlayerMaxHealth
  Set FriendlyMasterGhostHealthPercent = FriendlyMasterGhostHealth / 100.0
  Set EnemyMasterGhostHealthPercent = EnemyMasterGhostHealth / 100.0

Event UpdateManaInfo(Float CurrentMana, Float ManaIncreaseRate)
  Set CurrentManaValue = CurrentMana
  Set ManaIncreaseRateValue = ManaIncreaseRate

Event UpdateGameTimeInfo(Float RemainingTime)
  Set RemainingTimeValue = RemainingTime

Event UpdateGhostInfo(Integer FriendlyGhostCount, Integer EnemyGhostCount, Integer NeutralGhostCount)
  Set FriendlyGhostCountValue = FriendlyGhostCount
  Set EnemyGhostCountValue = EnemyGhostCount
  Set NeutralGhostCountValue = NeutralGhostCount
```

## 4. 必要なアセットの作成

### 4-1. アイコン素材の作成

以下のテクスチャを `/Game/UI/Textures/` フォルダに作成またはインポートします：

1. **T_PlayerIcon**
   - サイズ: 128x128
   - 用途: プレイヤーアイコン表示

2. **T_ManaIcon**
   - サイズ: 128x128
   - 色: 青系
   - 用途: マナ表示

3. **T_FriendlyGhost**
   - サイズ: 64x64
   - 色: 青系
   - 用途: 味方ゴースト表示

4. **T_NeutralGhost**
   - サイズ: 64x64
   - 色: 灰色系
   - 用途: 中立ゴースト表示

5. **T_EnemyGhost**
   - サイズ: 64x64
   - 色: 赤系
   - 用途: 敵ゴースト表示

### 4-2. フォントの設定

ゲームで使用するフォントを設定します：

1. エンジンのコンテンツブラウザで `/Engine/EngineFonts/` フォルダに移動
2. Robotoフォントのアセットをプロジェクトにコピー

あるいは、カスタムフォントをインポートする場合：
1. `/Game/UI/Fonts/` フォルダを作成
2. フォントファイル（.ttf）をインポート
3. Font Faceアセットを作成
4. Font設定で適切なサイズやスタイルを設定

## 5. GameModeとの連携

実際にメインHUDをゲーム中に表示するには以下の手順が必要です：

1. `/Game/Blueprints/Core/BP_ASGameMode` を開く
2. 「クラスのデフォルト」設定で `HUDClass` を `AASHUDBase` に設定
3. `/Game/Blueprints/Core/BP_ASHUDBase` を開く（なければ作成）
4. `MainHUDWidgetClass` プロパティを作成した `WBP_MainHUD` に設定

## 6. デバッグと最適化

HUDが正しく表示・動作するかを確認するために：

1. PIE（Play In Editor）モードでテスト
2. 様々な解像度設定でUIが崩れないか確認
3. パフォーマンスへの影響を確認（特に頻繁に更新される要素）
4. 異なる画面サイズでのスケーリングをテスト
