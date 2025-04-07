# エディタでの作成物一覧

## Input Actions
`/Game/Input/Actions/` フォルダに以下のInput Actionを作成します。

### 移動系
- `IA_Move`
  - Type: Value
  - Value Type: Vector2D
  - Description: WASDキーによる移動入力
  - Triggers: Any

- `IA_Look`
  - Type: Value
  - Value Type: Vector2D
  - Description: マウスによる視点移動
  - Triggers: Any

- `IA_Jump`
  - Type: Button
  - Description: ジャンプ
  - Triggers: Pressed, Released

### アクション系
- `IA_Dash`
  - Type: Button
  - Description: ダッシュ移動
  - Triggers: Pressed, Released

- `IA_Attack`
  - Type: Button
  - Description: 基本攻撃
  - Triggers: Pressed, Released

## Input Mapping Context
`/Game/Input/IMC_Default` を作成し、以下のマッピングを設定します。

### 移動系
- Move
  - W: +Y軸 (1.0)
  - S: -Y軸 (-1.0)
  - D: +X軸 (1.0)
  - A: -X軸 (-1.0)

- Look
  - Mouse X: X軸 (Scale: 1.0)
  - Mouse Y: Y軸 (Scale: -1.0)

- Jump
  - Space

### アクション系
- Dash
  - Left Shift

- Attack
  - Left Mouse Button

## Input Config Data Asset
`/Game/Input/DA_DefaultInputConfig` を作成し、以下の設定を行います。

### Input Actions の割り当て
- InputMove → IA_Move
- InputLook → IA_Look
- InputJump → IA_Jump
- InputDash → IA_Dash
- InputAttack → IA_Attack

## Blueprint設定

### BP_ASMasterCharacter
1. `/Game/Characters/BP_ASMasterCharacter` を作成
2. Parent Classを `ASMasterCharacter` に設定
3. 以下のプロパティを設定：
   - Default Mapping Context → IMC_Default
   - Move Action → IA_Move
   - Look Action → IA_Look
   - Jump Action → IA_Jump
   - Dash Action → IA_Dash
   - Attack Action → IA_Attack 

## AI設定

### Blackboard
`/Game/AI/BB_Servant` を作成し、以下のキーを設定：

- Target (Object)
  - Base Class: Actor
  - 説明: 攻撃対象

- HomeLocation (Vector)
  - 説明: 初期位置/待機位置

- PatrolLocation (Vector)
  - 説明: パトロール目的地

- GroupID (Int)
  - 説明: 所属グループID

### Behavior Tree
`/Game/AI/BT_Servant` を作成し、以下の構造で設定：

```
Root
├── Blackboard Decorator (Target IS SET)
│   └── Sequence
│       ├── BTService_DetectEnemies
│       │   - DetectionRange: 1000
│       │   - DetectionAngle: 90
│       ├── Move To (Target)
│       │   - Acceptance Radius: 200
│       └── BTTask_PerformAttack
└── Sequence
    ├── Move To (PatrolLocation)
    │   - Acceptance Radius: 100
    └── Wait (3.0s)
```

### Blueprint設定

#### BP_ASServantCharacter
1. `/Game/Characters/BP_ASServantCharacter` を作成
2. Parent Classを `ASServantCharacter` に設定
3. 以下のプロパティを設定：
   - AI Class → ASServantAIController
   - Behavior Tree → BT_Servant
   - Default Stats:
     - Max Health: 100
     - Attack Power: 20
     - Defense: 10
     - Attack Range: 200

## UI設定

### ゴースト占領ゲージ
1. `/Game/UI/WBP_GhostGauge` を作成
   - Parent Class: ASGhostGaugeWidget
   - サイズ: 200x80

2. デザイン設定
   - Size Boxを追加
     - Width Override: 200
     - Height Override: 80
   - Size Boxの中にVertical Boxを追加
     - Padding: 2
     - Size Fill: 1.0
   - Vertical Boxの中に4つのProgress Barを追加
     - 名前を「TeamAGauge」に設定（1つ目）
       - Height: 15
       - Fill Type: Left to Right
       - Background: 透明度0.5の黒
     - 名前を「TeamBGauge」に設定（2つ目）
       - Height: 15
       - Fill Type: Left to Right
       - Background: 透明度0.5の黒
     - 名前を「TeamCGauge」に設定（3つ目）
       - Height: 15
       - Fill Type: Left to Right
       - Background: 透明度0.5の黒
     - 名前を「TeamDGauge」に設定（4つ目）
       - Height: 15
       - Fill Type: Left to Right
       - Background: 透明度0.5の黒

3. チームカラーの設定
   - Details パネルで以下の色を設定：
     - Team A Color: 赤 (R:1.0, G:0.0, B:0.0, A:1.0)
     - Team B Color: 青 (R:0.0, G:0.0, B:1.0, A:1.0)
     - Team C Color: 緑 (R:0.0, G:1.0, B:0.0, A:1.0)
     - Team D Color: 黄 (R:1.0, G:1.0, B:0.0, A:1.0)

4. BP_ASGhostBaseの設定
   - `/Game/Ghosts/BP_ASGhostBase` を開く
   - 以下のプロパティを設定：
     - Gauge Widget Class → WBP_GhostGauge
     - Gauge Widget Component:
       - Draw Size: 200x80
       - Space: Screen
       - Draw at Desired Size: True

5. BP_ASMasterGhostの設定
   - `/Game/Ghosts/BP_ASMasterGhost` を開く
   - 親クラスの設定を継承（特別な設定は不要） 

## アニメーション通知の設定

### BP_ASMasterCharacter
1. `/Game/Characters/BP_ASMasterCharacter` を開く
2. 攻撃アニメーションモンタージュを開き、以下の通知を追加：
   - EnableCollision: コリジョンを有効にするタイミングで追加
   - DisableCollision: コリジョンを無効にするタイミングで追加

3. Blueprintで以下のイベントを設定：
   - EnableCollision通知を受け取った際に`EnableAttackCollision`関数を呼び出す
   - DisableCollision通知を受け取った際に`DisableAttackCollision`関数を呼び出す

### BP_ASServantCharacter
1. `/Game/Characters/BP_ASServantCharacter` を開く
2. 攻撃アニメーションモンタージュを開き、以下の通知を追加：
   - EnableCollision: コリジョンを有効にするタイミングで追加
   - DisableCollision: コリジョンを無効にするタイミングで追加

3. Blueprintで以下のイベントを設定：
   - EnableCollision通知を受け取った際に`EnableAttackCollision`関数を呼び出す
   - DisableCollision通知を受け取った際に`DisableAttackCollision`関数を呼び出す

## オルガン画面のエディタ作業内容

### 1. オルガン画面ウィジェットの作成
- **目的**: プレイヤーがサーヴァントを召喚し、指示を出すためのインターフェースを提供する。
- **内容**:
  - サーヴァントの情報を表示するためのウィジェットを作成。
  - サーヴァントの選択メニューを含む。

### 2. サーヴァント選択メニューの実装
- **目的**: プレイヤーが召喚したいサーヴァントを選択できるようにする。
- **内容**:
  - サーヴァントのリストを表示し、選択可能にする。

### 3. 移動先選択機能の実装
- **目的**: プレイヤーが移動先のゴーストを選択できるようにする。
- **内容**:
  - 移動先のゴーストを選択するためのインターフェースを作成。

### 4. 情報表示エリアの作成
- **目的**: サーヴァントや敵の状態、位置を表示する。
- **内容**:
  - サーヴァントや敵の情報をリアルタイムで表示するエリアを作成。

### 5. 操作ガイドの作成
- **目的**: オルガン画面の操作方法をプレイヤーに説明する。
- **内容**:
  - 操作方法を示すテキストやアイコンを含むガイドを作成。