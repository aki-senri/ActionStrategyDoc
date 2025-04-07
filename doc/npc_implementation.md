# NPC実装仕様書

## 1. 概要
NPCプレイヤーは、人間プレイヤーと同様にマナを管理し、サーヴァントを召喚して戦略的にゴーストを占領することを目指します。

## 2. 基本構成

### 2.1 主要クラス
- `AASNPCController`: NPCの基本動作を制御
- `UASNPCStrategyManager`: 戦略的な意思決定を管理

### 2.2 更新サイクル
- 定期的な意思決定更新（デフォルト：2秒間隔）
- マナ状況に応じたサーヴァント召喚判断
- 戦況評価に基づく戦略決定

## 3. 実装済み機能

### 3.1 基本システム
- NPCコントローラーの基本フレームワーク
- 戦略マネージャーの基本構造
- マナ管理システム
- 基本的なゴースト評価システム

### 3.2 戦略評価
- ゴースト占領状況の評価
- 基本的な戦略価値計算
- 単純な敵脅威度評価

## 4. TODO項目

### 4.1 サーヴァント選択システム
```cpp
// ASNPCStrategyManager::SelectBestServantType()の実装
- 戦況スコアに基づくサーヴァント選択ロジック
- 各サーヴァントタイプの特性評価
- 状況に応じた最適なサーヴァントタイプの決定
- サーヴァントタイプのバランス管理
```

### 4.2 ゴースト評価システムの拡張
```cpp
// ASNPCStrategyManager::CalculateGhostStrategicValue()の拡張
- ゴーストの位置評価
  - マップ中央からの距離計算
  - 戦略的重要地点の定義
- 周辺ゴーストの状態評価
  - 隣接ゴーストの支配状況
  - 連結性の評価
- マスターゴーストからの距離評価
  - 防衛ラインの考慮
  - 攻撃経路の評価
```

### 4.3 敵脅威評価システム
```cpp
// ASNPCStrategyManager::EvaluateEnemyThreat()の実装
- 敵サーヴァント分析
  - 数と種類の評価
  - 配置状況の分析
- 敵占領地域の評価
  - 支配地域のパターン認識
  - 潜在的な攻撃経路の特定
- 敵マスターの位置追跡
  - 行動パターンの分析
  - 危険区域の特定
```

### 4.4 召喚ポイント選択の最適化
```cpp
// AASNPCController::SelectBestSummonPoint()の拡張
- 戦略的位置の評価
- 既存サーヴァントとの連携考慮
- 敵からの安全性評価
```

### 4.5 マナ管理の高度化
```cpp
// AASNPCController::ShouldUseManaForSummon()の拡張
- 状況に応じた維持マナ量の動的調整
- 将来の必要マナ量の予測
- 効率的なマナ使用戦略の実装
```

## 5. 実装優先順位

1. サーヴァント選択システム
   - 基本的な戦闘能力の実現に必須
   - ゲームバランスへの直接的影響

2. ゴースト評価システム
   - 戦略的な意思決定の基盤
   - 効果的な領域支配の実現

3. 敵脅威評価システム
   - 適応的な戦略決定に必要
   - 防衛と攻撃のバランス調整

4. 召喚ポイント選択
   - 効率的なユニット配置の実現
   - 戦術的優位性の確保

5. マナ管理の高度化
   - リソース効率の最適化
   - 長期的な戦略の実現

## 6. 実装指針

### 6.1 コーディング規約
- UE5のコーディング規約に準拠
- 適切なコメント記述
- パフォーマンスを考慮した実装

### 6.2 拡張性
- 新しいサーヴァントタイプへの対応
- 戦略パターンの追加容易性
- AIの難易度調整機能

### 6.3 デバッグ・調整
- 戦略決定過程のログ出力
- パラメータの調整機能
- 戦略評価結果の可視化

## 7. 性能要件

### 7.1 更新頻度
- 意思決定更新: 2秒間隔（調整可能）
- 状況評価: 毎フレーム不要、定期更新で十分

### 7.2 処理負荷
- 戦略計算は重くならないよう注意
- キャッシュの活用
- 必要に応じた計算頻度の調整

## 8. テスト項目

### 8.1 機能テスト
- サーヴァント選択ロジックの検証
- ゴースト評価の正確性確認
- マナ管理の適切性確認

### 8.2 統合テスト
- 全体的な戦略の整合性
- 他システムとの連携確認
- パフォーマンス検証

### 8.3 バランステスト
- 難易度別の動作確認
- 様々な状況での戦略適応性
- プレイヤーとの対戦バランス

## 9. 実装段階

### Phase 1: 基本システムの実装
#### 1.1 サーヴァント選択システムの基本実装
```cpp
// ASNPCStrategyManager::SelectBestServantType()
- サーヴァントタイプの列挙定義
- 基本的な選択ロジックの実装
  - 近接型: 初期で呼び出しを行う、また相手のサーヴァントに機動型が多い場合に呼び出しを行う
  - 機動型: 相手のサーヴァントに装甲型が多い場合に呼び出しを行う
  - 装甲型: 相手のサーヴァントに近接型が多い場合に呼び出しを行う
- サーヴァントコスト管理の実装
```

#### 1.2 基本的なゴースト評価
```cpp
// ASNPCStrategyManager::CalculateGhostStrategicValue()
- 基本的な価値計算の実装
  - 占領状態による価値調整
  - 単純な距離評価
    - マスターゴーストか、マスターゴーストに近いゴーストに敵がいる場合は優先度を高くする
- 優先度によるゴーストのソート
```

#### 1.3 マナ管理の基本実装
```cpp
// AASNPCController::ShouldUseManaForSummon()
- 基本的なマナ閾値の設定
- 単純なマナ消費判断
- マナ回復予測
```

### Phase 2: 戦略システムの拡張
#### 2.1 高度なサーヴァント選択
```cpp
// ASNPCStrategyManager
- サーヴァントタイプごとの特性データ定義
struct FServantTypeData
{
    float AttackPower;
    float Defense;
    float Mobility;
    float Cost;
    float SpecialAbilityValue;
};

- 状況に応じた選択ロジック
float EvaluateServantTypeForSituation(
    TSubclassOf<ASServantCharacter> ServantType,
    const FBattleSituation& Situation
);
```

#### 2.2 詳細なゴースト評価
```cpp
// ASNPCStrategyManager
- マップ位置評価の実装
struct FGhostPositionValue
{
    float CentralityValue;
    float StrategicPointValue;
    float ConnectivityValue;
};

- 周辺状態評価
struct FGhostEnvironmentValue
{
    float NearbyAlliesValue;
    float NearbyEnemiesValue;
    float PathImportanceValue;
};
```

#### 2.3 敵脅威評価システム
```cpp
// ASNPCStrategyManager
- 敵ユニット分析
struct FEnemyThreatAnalysis
{
    TArray<FEnemyUnitInfo> Units;
    float TotalThreatLevel;
    FVector2D ThreatDirection;
};

- 領域支配パターン認識
struct FTerritoryPattern
{
    TArray<ASGhostBase*> ControlledGhosts;
    float DominanceLevel;
    bool bIsExpanding;
};
```

### Phase 3: AI行動の最適化
#### 3.1 動的戦略調整
```cpp
// ASNPCStrategyManager
- 戦況に応じた戦略変更
enum class EBattleStrategy
{
    Aggressive,    // 積極的な攻撃
    Defensive,     // 防衛重視
    Balanced,      // バランス型
    Expanding      // 領域拡大
};

- 戦略決定ロジック
void UpdateBattleStrategy(const FBattleSituation& Situation);
```

#### 3.2 リソース最適化
```cpp
// ASNPCController
- マナ使用の最適化
struct FResourcePlan
{
    float ReservedMana;
    float PlannedExpenditure;
    TArray<FPlannedAction> Actions;
};

- 行動計画の生成
TArray<FPlannedAction> GenerateActionPlan(float AvailableMana);
```

#### 3.3 召喚ポイント最適化
```cpp
// AASNPCController
- 召喚位置の戦略的選択
struct FSummonPointEvaluation
{
    AASSummonPoint* Point;
    float StrategicValue;
    float SafetyLevel;
    float EfficiencyScore;
};

- 位置評価ロジック
FSummonPointEvaluation EvaluateSummonPoint(
    AASSummonPoint* Point,
    const FBattleSituation& Situation
);
```

### Phase 4: システム統合とバランス調整
#### 4.1 デバッグシステム
```cpp
// ASNPCDebugComponent
- 戦略決定の可視化
struct FStrategyDebugInfo
{
    EBattleStrategy CurrentStrategy;
    TArray<FString> DecisionFactors;
    float ConfidenceLevel;
};

- パフォーマンスモニタリング
struct FAIPerformanceMetrics
{
    float DecisionTime;
    int32 EvaluationsPerSecond;
    float ResourceEfficiency;
};
```

#### 4.2 難易度調整システム
```cpp
// ASNPCDifficultyManager
- 難易度パラメータ
struct FAIDifficultyParams
{
    float DecisionAccuracy;
    float ReactionTime;
    float ResourceEfficiency;
    float TacticalAwareness;
};

- 動的難易度調整
void AdjustDifficulty(const FPlayerPerformance& PlayerStats);
```

#### 4.3 バランステスト用ツール
```cpp
// ASNPCBalanceTool
- テストシナリオ
struct FTestScenario
{
    TArray<FBattleSituation> Situations;
    TArray<FExpectedResponse> ExpectedResponses;
    float SuccessRate;
};

- 性能評価
struct FPerformanceReport
{
    float WinRate;
    float ResourceEfficiency;
    float StrategicSuccess;
    TArray<FString> Recommendations;
};
```

### Phase 5: 最適化とポリッシュ
#### 5.1 パフォーマンス最適化
```cpp
// 全体的な最適化
- 計算負荷の軽減
- メモリ使用の最適化
- キャッシュシステムの実装
```

#### 5.2 エラー処理
```cpp
// エラーハンドリングの実装
- 無効な状態のチェック
- リカバリーメカニズム
- エラーログシステム
```

#### 5.3 ドキュメント化
```cpp
// ドキュメント更新
- API仕様書
- チューニングガイド
- デバッグガイド
```

## 10. 実装スケジュール

### Week 1-2: Phase 1
- 基本システムの実装
- 単体テスト
- 基本機能の検証

### Week 3-4: Phase 2
- 戦略システムの実装
- 統合テスト
- 初期バランス調整

### Week 5-6: Phase 3
- AI行動の最適化
- パフォーマンステスト
- 動作検証

### Week 7-8: Phase 4
- システム統合
- 総合テスト
- バランス調整

### Week 9-10: Phase 5
- 最終調整
- ドキュメント作成
- リリース準備
