# サーヴァントAI実装手順

## 1. 概要
このドキュメントでは、サーヴァントのAI実装手順について説明します。

## 2. 実装の流れ

### 2.1 ブラックボードの設定
1. ブラックボードデータアセットの作成
   - Content/Game/AI/Blackboards/BB_Servant を作成
   - 以下のキーを設定：
     - TargetActor (Object) : 攻撃対象
     - DestinationLocation (Vector) : 移動目標地点
     - CurrentGhost (Object) : 現在のゴースト
     - TargetGhost (Object) : 目標のゴースト
     - GroupID (Int) : 所属グループID
     - CombatState (Enum) : 戦闘状態
     - PatrolLocation (Vector) : 巡回地点
     - IsWaiting (Bool) : 待機状態かどうか
     - HealthPercentage (Float) : HP割合

### 2.2 C++クラスの作成
1. ブラックボードキー定義
   - Source/ActionStrategyAllAi/Public/AI/ASServantBlackboardKeys.h

2. AIコントローラー
   - Source/ActionStrategyAllAi/Public/AI/ASServantAIController.h
   - Source/ActionStrategyAllAi/Private/AI/ASServantAIController.cpp

3. 基本タスク
   - Source/ActionStrategyAllAi/Public/AI/Tasks/ASBTTask_MoveToGhost.h
   - Source/ActionStrategyAllAi/Private/AI/Tasks/ASBTTask_MoveToGhost.cpp
   - Source/ActionStrategyAllAi/Public/AI/Tasks/ASBTTask_Attack.h
   - Source/ActionStrategyAllAi/Private/AI/Tasks/ASBTTask_Attack.cpp

4. サービス
   - Source/ActionStrategyAllAi/Public/AI/Services/ASBTService_DetectEnemies.h
   - Source/ActionStrategyAllAi/Private/AI/Services/ASBTService_DetectEnemies.cpp
   - Source/ActionStrategyAllAi/Public/AI/Services/ASBTService_CheckHealth.h
   - Source/ActionStrategyAllAi/Private/AI/Services/ASBTService_CheckHealth.cpp
5. デコレーター
   - Source/ActionStrategyAllAi/Public/AI/Decorators/ASBTDecorator_IsAtLocationDistance.h
   - Source/ActionStrategyAllAi/Private/AI/Decorators/ASBTDecorator_IsAtLocationDistance.cpp
     - パスによらない距離チェック

### 2.3 ビヘイビアツリーの構築
1. ビヘイビアツリーアセットの作成
   - Content/Game/AI/BehaviorTrees/BT_Servant を作成

2. ビヘイビアツリーの構造
   1. 近くの敵チェック
   2. 近くに敵がいたら攻撃対象として選択
      1. 攻撃対象を攻撃
   3. 近くに占拠してないゴーストがあれば、そのゴーストを攻撃対象として選択
      1. 攻撃対象を攻撃
   4. 移動先指定があり、かつ移動先に到達していない場合は移動
   5. 移動先指定があり、かつ移動先に到達している場合
      1. 移動先があれば更新
      2. なければ待機
   6. 移動先指定がない場合は、近くのゴーストを占領

```
Root
├── シーケンスノード
│   ├── 敵検知サービス
│   └── セレクターノード
│       ├── シーケンスノード（近くの敵への対応）
│       │   ├── 条件：近くに敵が存在する
│       │   ├── 攻撃対象の選択
│       │   └── 攻撃実行
│       ├── シーケンスノード（未占拠ゴーストへの対応）
│       │   ├── 条件：近くに未占拠ゴーストが存在する
│       │   ├── ゴーストを攻撃対象として選択
│       │   └── 攻撃実行
│       ├── シーケンスノード（移動先指定がある場合）
│       │   ├── 条件：移動先が指定されている
│       │   ├── 条件：移動先に未到達
│       │   └── 移動実行
│       ├── シーケンスノード（待機）
│       │   ├── 条件：移動先が指定されている
│       │   ├── 条件：移動先に到達済み
│       │   └── 待機実行
│       └── シーケンスノード（ゴースト占領）
│           ├── 条件：移動先指定なし
│           ├── 近くのゴースト探索
│           └── ゴースト占領実行
```

### 2.4 デバッグ機能の実装
1. AIデバッグ表示の設定
   - ShowDebug AI コマンドの有効化
   - ブラックボード値の可視化
   - 行動状態の表示

2. エディタでのデバッグ支援機能
   - ビヘイビアツリーエディタでのデバッグ表示
   - ブラックボード値のウォッチ

## 3. テスト手順
1. 基本動作テスト
   - 移動処理の確認
   - 攻撃処理の確認
   - 待機処理の確認

2. グループ行動テスト
   - グループでの移動
   - グループでの戦闘
   - グループの合流

3. 戦略的行動テスト
   - ゴースト占領行動
   - 撤退判断
   - 敵への対応

## 4. 注意点
1. パフォーマンス
   - 敵検知の更新頻度は0.5秒程度に設定
   - 重い処理は非同期実行を検討

2. 拡張性
   - 新しい行動の追加が容易な構造を維持
   - サーヴァントタイプごとの差異化を考慮

3. デバッグ
   - エラー発生時のログ出力を適切に設定
   - デバッグ表示の切り替えを容易に 