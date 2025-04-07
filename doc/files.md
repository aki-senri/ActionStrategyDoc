# プロジェクト構造

```
ActionStrategyAllAi/
├── Source/
│   ├── ActionStrategyAllAi/
│   │   ├── Public/
│   │   │   ├── Core/
│   │   │   │   ├── ASGameMode.h          # ゲームのルール、勝利条件、マナの基本生成を管理
│   │   │   │   ├── ASGameState.h         # ゲームの状態、占領ゴースト数、経過時間を管理
│   │   │   │   └── ASPlayerState.h       # プレイヤーのマナ、所持サーヴァント情報を管理
│   │   │   ├── Characters/
│   │   │   │   ├── ASCharacterBase.h     # キャラクターの基本機能（移動、ジャンプ、ダッシュ）
│   │   │   │   ├── ASMasterCharacter.h   # プレイヤーキャラクター
│   │   │   │   └── ASServantCharacter.h  # サーヴァントの基本クラス
│   │   │   ├── Input/
│   │   │   │   ├── ASInputConfig.h       # 入力設定データアセット
│   │   │   │   └── ASInputManager.h      # 入力管理システム
│   │   │   ├── AI/
│   │   │   │   ├── ASServantAIController.h  # サーヴァントのAI制御
│   │   │   │   ├── Tasks/                   # AIのBehavior Tree用のカスタムタスク
│   │   │   │   └── Services/                # AIのサービス（周辺の敵の検知など）
│   │   │   ├── Ghost/
│   │   │   │   ├── ASGhostBase.h        # ゴーストの基本機能（占領システム、回復機能）
│   │   │   │   ├── ASMasterGhost.h      # マスターゴースト特有の機能
│   │   │   │   └── ASNormalGhost.h     # 中立ゴースト特有の機能
│   │   │   ├── Components/
│   │   │   │   ├── ASManaComponent.h         # マナの管理、生成、消費を制御
│   │   │   │   ├── ASServantManager.h        # サーヴァントのグループ化と指示を管理
│   │   │   │   ├── ASServantGroupTypes.h     # サーヴァントのグループ化と指示の型定義
│   │   │   │   └── ASGhostCaptureComponent.h # ゴーストの占領状態を管理
│   │   │   ├── Spawn/
│   │   │   │   └── ASSummonPoint.h      # サーヴァントの召喚ポイントを管理
│   │   │   └── UI/
│   │   │       ├── ASHUDBase.h          # ゲーム中のHUD管理
│   │   │       └── Widgets/             # 各種UIウィジェット
│   │   │           ├── ASMainHUDWidget.h       # メインHUDウィジェット
│   │   │           ├── ASOrganWidget.h         # オルガン画面ウィジェット
│   │   │           └── ASNotificationWidget.h  # 通知表示ウィジェット
│   │   └── Private/
│   │       └── (上記と同じ構造)
│   └── ActionStrategyAllAiEditor/
├── Content/
│   └── Game/
│       ├── Characters/
│       │   ├── Master/
│       │   │   ├── Animations/          # マスターキャラクターのアニメーション
│       │   │   ├── Materials/           # マスターキャラクター用マテリアル
│       │   │   ├── Meshes/             # マスターキャラクターの3Dモデル
│       │   │   └── Blueprints/         # マスターキャラクター関連のブループリント
│       │   └── Servants/
│       │       ├── Animations/          # サーヴァントのアニメーション
│       │       ├── Materials/           # サーヴァント用マテリアル
│       │       ├── Meshes/             # サーヴァントの3Dモデル
│       │       └── Blueprints/         # サーヴァント関連のブループリント
│       ├── Maps/
│       │   ├── MainMenu/               # メインメニューマップとそのアセット
│       │   └── Gameplay/               # ゲームプレイマップとそのアセット
│       ├── Effects/
│       │   ├── Particles/              # パーティクルエフェクト
│       │   ├── Materials/              # エフェクト用マテリアル
│       │   └── Blueprints/            # エフェクト関連のブループリント
│       ├── UI/
│       │   ├── HUD/                    # HUD関連のウィジェット
│       │   ├── Menu/                   # メニュー関連のウィジェット
│       │   └── Common/                 # 共通UIコンポーネント
│       ├── Input/
│       │   ├── Actions/                # Input Action定義
│       │   ├── Contexts/               # Input Mapping Context
│       │   └── Configs/                # 入力設定データアセット
│       ├── AI/
│       │   ├── BehaviorTrees/          # AIのビヘイビアツリー
│       │   ├── Blackboards/            # AIのブラックボード
│       │   └── Tasks/                  # カスタムAIタスク
│       └── Blueprints/
│           ├── Core/                   # コアゲームシステムのブループリント
│           ├── Ghost/                  # ゴーストシステム関連のブループリント
│           └── Components/             # 共通コンポーネントのブループリント
└── Config/
    ├── DefaultEngine.ini
    ├── DefaultGame.ini
    └── DefaultInput.ini

# 主要クラスの説明

## Core
- `AASGameMode`: ゲームのルール管理、勝利判定、マナ生成ロジック
- `AASGameState`: ゲームの進行状態、占領状況、時間管理
- `AASPlayerState`: プレイヤーの状態管理（マナ、サーヴァント情報）

## Characters
- `AASCharacterBase`: 全キャラクターの基本機能
- `AASMasterCharacter`: プレイヤーキャラクター専用機能
- `AASServantCharacter`: サーヴァントの基本機能
  - 派生クラス: `AASMeleeServant`, `AASMobileServant`, `AASArmoredServant`

## AI
- `AASServantAIController`: サーヴァントAI制御
- `Tasks`: カスタムBehaviorTreeタスク
- `Services`: AI用サービス機能

## Ghost
- `ASGhostBase`: ゴースト基本機能
- `ASMasterGhost`: マスターゴースト機能
- `ASNeutralGhost`: 中立ゴースト機能

## Components
- `AASManaComponent`: マナシステム管理
- `ASGhostCaptureComponent`: 占領システム管理
- `AASServantGroupComponent`: グループ管理システム

## Spawn
- `ASSummonPoint`: サーヴァントの召喚ポイントを管理
  - マスターゴースト周辺の6箇所の召喚ポイントを管理
  - 召喚順序の制御
  - 召喚時間（10秒/体）の管理

## UI
- `AASHUDBase`: HUD管理
  - ゲーム内表示全体の制御
  - オルガン画面の表示切り替え
  - 通知システムの管理
- `ASMainHUDWidget`: メインHUDウィジェット
  - プレイヤー情報（HP、マナなど）表示
  - ゲーム状態（時間、ゴースト占領数）表示
  - 各種ステータスバーの制御
- `ASOrganWidget`: オルガン画面ウィジェット
  - マップ全体表示
  - サーヴァント選択
  - 移動先ゴースト選択と指示
- `ASNotificationWidget`: 通知表示ウィジェット
  - ゲーム内イベント通知
  - ゴースト占領、サーヴァント召喚などの通知

## Input
- `ASInputConfig`: Enhanced Input Systemの入力アクション設定を管理
- `ASInputManager`: 入力システムの初期化、カスタマイズ、設定の保存/読み込みを管理
