# HUDとウィジェット実装ガイド

## 実装アプローチ

このプロジェクトでは、C++とブループリントを組み合わせたハイブリッドアプローチを採用しています：

1. C++で基本的なロジックとクラス構造を定義
2. ブループリントで視覚的なレイアウトとデザインを実装
3. UMGを活用したUIコンポーネントの作成

## ウィジェットの説明

### AASHUDBase
- **役割**: ゲーム内HUDの管理と制御
- **C++実装**:
  - ウィジェットのライフサイクル管理
  - 表示/非表示の制御
  - AASPlayerState, AASGameStateとの連携
- **Blueprint実装**:
  - 特殊エフェクトの追加
  - アニメーションの定義

### UASMainHUDWidget
- **役割**: 常時表示されるメインHUD
- **C++実装**:
  - HP, マナ, ゲーム時間の表示ロジック
  - AASPlayerState, AASGameStateからのデータ取得
- **Blueprint実装**:
  - レイアウトデザイン
  - 色とスタイルの設定
  - アニメーションとトランジション

### USAServantWidget
- **役割**: サーヴァントの情報表示。アイコン、HPの表示。
- **C++実装**:
  - サーヴァントのアイコン、HPの表示ロジック
  - サーヴァントへのマウスオーバーイベント
- **Blueprint実装**:
  - レイアウトデザイン
  - アニメーション

### UASOrganWidget
- **役割**: オルガン画面（マップ&サーヴァント管理）
- **C++実装**:
  - ゴースト状態の追跡
  - サーヴァント選択ロジック
  - 移動指示処理
- **Blueprint実装**:
  - マップビューの視覚化
  - サーヴァントアイコンの表示
  - インタラクション効果

### UASNotificationWidget
- **役割**: ゲーム内通知システム
- **C++実装**:
  - 通知キューの管理
  - 表示時間の制御
  - 優先度システム
- **Blueprint実装**:
  - 通知アニメーション
  - スタイルとレイアウト
  - サウンドとの連携

## 実装手順

1. C++クラスの作成（完了）
2. ブループリントクラスの派生
   - 各C++クラスからのブループリント拡張
   - Widget Blueprintでの視覚的設計
3. PlayerControllerとの連携
   - AASHUDBaseの初期化
   - 入力処理の設定
4. DataBindingの設定
   - プロパティバインディングでデータ同期
   - イベントによる更新の最小化

## ウィジェット間通信

- **直接参照**: AASHUDBaseが各ウィジェットへの参照を維持
- **デリゲート**: 状態変更時のイベント通知
- **インターフェース**: 汎用的な通信手法

## パフォーマンス考慮事項

- 不必要な毎フレーム更新を避ける
- Canvasスロットの最適化
- 条件付きバインディングの活用 

# オルガン画面のBlueprint実装仕様

## 1. 画面モードと状態遷移

### 1.1 オルガンモード定義
```
1. Default
   - 初期状態
   - サーヴァント選択が可能

2. GroupMoveOrder
   - 既存グループへの移動指示モード
   - 選択されたグループの情報表示
   - 自動的にGhostSelectionモードへ遷移

3. ServantSummoning
   - 新規サーヴァント召喚モード
   - 選択されたサーヴァントの情報表示
   - マナコストと召喚可否の確認
   - 自動的にGhostSelectionモードへ遷移

4. GhostSelection
   - 移動先ゴースト選択モード
   - 経路プレビュー表示
   - 移動先の確定/キャンセル
```

### 1.2 状態遷移フロー
```
1. 既存グループへの移動指示:
   Default → GroupMoveOrder → GhostSelection → Default

2. 新規サーヴァント召喚:
   Default → ServantSummoning → GhostSelection → Default
```

## 2. UI構成

### 2.1 基本レイアウト
```
- Root Canvas Panel
  |- Map Canvas (マップ表示エリア)
  |- Path Preview Overlay (経路プレビュー表示)
  |- Resource Display (リソース表示)
```

### 2.2 必要なウィジェットコンポーネント
- MapCanvas (UCanvasPanel)
- PathPreviewOverlay (UOverlay)
- ResourcePanel (UVerticalBox)

## 3. イベント実装

### 3.1 OnOrganModeChanged
```
入力: NewMode (uint8)
処理:
1. モードに応じたUI表示切り替え
   - Default: 全体表示
   - GroupMoveOrder: グループ情報表示
   - ServantSummoning: サーヴァント情報表示
   - GhostSelection: 経路選択UI表示
2. 各UIパネルの表示/非表示制御
3. 選択状態のリセット
4. 経路プレビューのクリア
```

### 3.2 OnServantSelected
```
入力: ServantClass (TSubclassOf<AASServantCharacter>)
処理:
1. マナコストチェック
2. 召喚情報の設定
   - ServantClass
   - ServantCount
   - ManaCost
3. ServantSummoningモードへの遷移
```

### 3.3 OnServantGroupSelected
```
入力: GroupID (int32)
処理:
1. グループIDの保存
2. 現在の移動経路のクリア
3. GroupMoveOrderモードへの遷移
```

### 3.4 OnGhostSelected
```
入力: Ghost (AASGhostBase)
処理:
1. 選択されたゴーストの視覚的ハイライト
2. 現在のモードに応じた処理
   - ServantSummoning: 
     - 新規選択: 召喚位置として追加
     - 同じゴースト選択: 移動先確定、RequestServantSummon呼び出し
   - GroupMoveOrder: 
     - 新規選択: 移動先として追加
     - 同じゴースト選択: 移動先確定、RequestGroupMove呼び出し
3. 経路プレビューの更新
4. 移動先確定時はDefaultモードに戻る
```

### 3.5 OnUpdateMap
```
処理:
1. ゴーストの状態更新
   - チーム占領状態の色分け
   - HP表示更新
2. サーヴァントの位置更新
   - GetActiveServants()で取得したサーヴァントの位置を表示
3. 経路表示の更新
```

### 3.6 OnPathPreviewUpdated
```
処理:
1. 経路プレビューの表示更新
   - 移動経路のライン描画
   - 経由ポイントの表示
   - 順序番号の表示
2. 選択されたゴーストの表示更新
```

### 3.7 追加機能
```
1. ConfirmDestination
   - 現在のモードに応じた処理を実行
   - ServantSummoning: RequestServantSummon呼び出し
   - GroupMoveOrder: RequestGroupMove呼び出し
   - 処理後、Defaultモードに戻る

2. CancelLastDestination
   - 最後に選択したゴーストを削除
   - 経路プレビューを更新

3. CompleteDestinationSelection
   - 目的地選択を完了し、ConfirmDestinationを呼び出し
```

## 4. 視覚的フィードバック

### 4.1 選択状態の表示
```
- サーヴァント選択時: 選択枠表示、色調変更
- グループ選択時: ハイライト表示
- ゴースト選択時: 選択エフェクト表示
```

### 4.2 経路プレビュー
```
- 経路ライン: 矢印付きの線で表示
- 経由ポイント: 番号付きマーカー表示
- 最終目的地: 特別なマーカー表示
```

### 4.3 エラー表示
```
- マナ不足: 赤色でコスト表示、警告メッセージ
- グループ制限: 警告アイコン、メッセージ表示
- 無効な経路: 経路ラインを赤色表示
```

## 5. 入力処理

### 5.1 マウス操作
```
- 左クリック: 
  - Default: サーヴァントまたはグループの選択
  - GhostSelection: 
    - 新規ゴースト: 移動先として追加
    - 同じゴースト: 移動先確定
- 右クリック: 
  - GhostSelection: 最後に選択したゴーストをキャンセル（CancelLastDestination）
  - その他: キャンセル/戻る
- ホバー: ツールチップ表示
```

### 5.2 キーボードショートカット
```
- ESC: キャンセル/戻る
- TAB: オルガン画面切替
- Backspace:
  - GhostSelection: 最後に選択したゴーストをキャンセル（CancelLastDestination）
```

## 6. パフォーマンス最適化

### 6.1 描画最適化
```
- 非表示要素の更新停止
- 画面外要素のクリッピング
- 動的生成要素の再利用
```

### 6.2 更新処理の最適化
```
- 必要な場合のみ更新
- バッチ更新の利用
- タイマーによる更新間隔制御
``` 