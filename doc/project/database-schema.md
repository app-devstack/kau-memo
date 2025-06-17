# 飼うメモ - データベーステーブル設計書

## プロジェクト概要
家族やグループで共有する買い物リスト管理アプリです。キャラクター育成要素を取り入れ、楽しく計画的な買い物を促進します。

**技術仕様**
- データベース: SQLite (Expo SQLite)
- ID: テキスト形式（UUID v7）
- 日付: timestamp_ms（ミリ秒タイムスタンプ）

## テーブル構成

### 1. users（ユーザー管理）
アプリを利用するユーザーの基本情報を管理します。

| カラム名 | 型 | 必須 | 説明 |
|---------|----|----|------|
| id | text | ○ | ユーザーID（主キー・UUID） |
| email | text | ○ | メールアドレス（ユニーク） |
| username | text | ○ | ユーザー名 |
| password_hash | text | ○ | パスワードハッシュ |
| display_name | text | - | 表示名 |
| avatar_emoji | text | - | アバター絵文字 |
| timezone | text | - | タイムゾーン |
| created_at | integer | ○ | 作成日時 |
| updated_at | integer | ○ | 更新日時 |

### 2. groups（グループ管理）
家族やルームメイト等のグループ情報を管理します。

| カラム名 | 型 | 必須 | 説明 |
|---------|----|----|------|
| id | text | ○ | グループID（主キー・UUID） |
| name | text | ○ | グループ名 |
| description | text | - | グループ説明 |
| owner_id | text | ○ | オーナーユーザーID |
| invite_code | text | ○ | 招待コード（ユニーク） |
| is_active | integer | ○ | アクティブフラグ（0/1） |
| created_at | integer | ○ | 作成日時 |
| updated_at | integer | ○ | 更新日時 |

**is_activeについて**: グループの解散・凍結時に0に設定。データは保持されるため復活可能。

### 3. group_members（グループメンバー管理）
グループに参加しているユーザーの情報を管理します。

| カラム名 | 型 | 必須 | 説明 |
|---------|----|----|------|
| id | text | ○ | ID（主キー・UUID） |
| group_id | text | ○ | グループID |
| user_id | text | ○ | ユーザーID |
| role | text | ○ | 役割（owner/admin/member） |
| joined_at | integer | ○ | 参加日時 |
| is_active | integer | ○ | アクティブフラグ（0/1） |

**is_activeについて**: メンバーの脱退・除名時に0に設定。履歴は保持されるため再参加時の復旧が可能。

### 4. group_characters（キャラクター管理）
グループが育成するキャラクターの情報を管理します。

| カラム名 | 型 | 必須 | 説明 |
|---------|----|----|------|
| id | text | ○ | キャラクターID（主キー・UUID） |
| group_id | text | ○ | グループID |
| name | text | ○ | キャラクター名 |
| character_type | text | ○ | キャラクタータイプ（tanuki/cat/bear等） |
| level | integer | ○ | キャラクターレベル（デフォルト: 1） |
| experience | integer | ○ | 現在の経験値（デフォルト: 0） |
| happiness | integer | ○ | 幸福度（0-100、デフォルト: 50） |
| is_primary | integer | ○ | メインキャラクターフラグ（0/1） |
| is_active | integer | ○ | アクティブフラグ（0/1） |
| created_at | integer | ○ | 作成日時 |
| updated_at | integer | ○ | 更新日時 |

**is_activeについて**: 複数キャラクター対応時の切り替え用。非使用キャラクターは0に設定。

### 5. categories（カテゴリ管理）
買い物アイテムのカテゴリ情報を管理します。

| カラム名 | 型 | 必須 | 説明 |
|---------|----|----|------|
| id | text | ○ | カテゴリID（主キー・UUID） |
| name | text | ○ | カテゴリ名 |
| name_en | text | - | 英語名（システム用） |
| icon | text | ○ | アイコン識別子 |
| description | text | - | 説明文 |
| theme_color | text | - | テーマカラー識別子 |
| sort_order | integer | ○ | 表示順（デフォルト: 0） |
| is_system | integer | ○ | システムカテゴリフラグ（0/1） |
| created_at | integer | ○ | 作成日時 |

**フロントエンド連携**: icon、theme_colorはフロントエンド側で絵文字やCSSクラスにマッピングされます。

### 6. shopping_items（買い物アイテム管理）
メインの買い物リストアイテムを管理します。

| カラム名 | 型 | 必須 | 説明 |
|---------|----|----|------|
| id | text | ○ | アイテムID（主キー・UUID） |
| group_id | text | ○ | グループID |
| name | text | ○ | アイテム名 |
| category_id | text | - | カテゴリID |
| is_completed | integer | ○ | 完了フラグ（0/1、デフォルト: 0） |
| buy_date | text | - | 購入予定日（YYYY-MM-DD形式） |
| is_deleted | integer | ○ | 削除フラグ（0/1、デフォルト: 0） |
| priority | integer | ○ | 優先度（デフォルト: 0） |
| memo | text | - | メモ |
| completed_by | text | - | 完了者ユーザーID |
| completed_at | integer | - | 完了日時 |
| deleted_by | text | - | 削除者ユーザーID |
| deleted_at | integer | - | 削除日時 |
| due_date | text | - | 期限日（YYYY-MM-DD形式） |
| created_at | integer | ○ | 作成日時 |
| updated_at | integer | ○ | 更新日時 |

**buy_dateについて**:
- NULL: いつでも良い
- 今日の日付: 今日買う予定
- 未来の日付: 指定日に買う予定
- 過去の日付: 予定が過ぎている（要注意アイテム）

### 7. group_achievements（グループ実績管理）
グループ・キャラクター単位での実績・バッジを管理します。

| カラム名 | 型 | 必須 | 説明 |
|---------|----|----|------|
| id | text | ○ | 実績ID（主キー・UUID） |
| group_id | text | ○ | グループID |
| character_id | text | - | キャラクターID |
| achievement_key | text | ○ | 実績キー |
| achievement_name | text | ○ | 実績名 |
| description | text | - | 実績説明 |
| badge_emoji | text | - | バッジ絵文字 |
| experience_reward | integer | ○ | 報酬経験値（デフォルト: 0） |
| unlocked_by | text | ○ | 取得者ユーザーID |
| unlocked_at | integer | ○ | 取得日時 |

### 8. group_daily_stats（グループ日次統計）
グループの日々の利用統計を管理します。

| カラム名 | 型 | 必須 | 説明 |
|---------|----|----|------|
| id | text | ○ | 統計ID（主キー・UUID） |
| group_id | text | ○ | グループID |
| date | text | ○ | 対象日（YYYY-MM-DD形式） |
| items_added | integer | ○ | 追加アイテム数（デフォルト: 0） |
| items_completed | integer | ○ | 完了アイテム数（デフォルト: 0） |
| lists_organized | integer | ○ | リスト整理回数（デフォルト: 0） |
| total_experience_gained | integer | ○ | 獲得経験値合計（デフォルト: 0） |
| active_users | integer | ○ | アクティブユーザー数（デフォルト: 0） |
| created_at | integer | ○ | 作成日時 |

## 外部キー関係

### 主要な関連
- **groups.owner_id** → users.id
- **group_members.group_id** → groups.id
- **group_members.user_id** → users.id
- **group_characters.group_id** → groups.id
- **shopping_items.group_id** → groups.id
- **shopping_items.category_id** → categories.id
- **shopping_items.completed_by** → users.id
- **shopping_items.deleted_by** → users.id
- **group_achievements.group_id** → groups.id
- **group_achievements.character_id** → group_characters.id
- **group_achievements.unlocked_by** → users.id
- **group_daily_stats.group_id** → groups.id

## 経験値獲得システム

キャラクター育成において、以下のアクションで経験値を獲得できます：

| アクション | 獲得経験値 | 説明 |
|----------|-----------|------|
| アイテム追加 | +5 | 買い物リストにアイテムを追加 |
| アイテム完了 | +10 | アイテムを購入完了 |
| リスト整理 | +15 | カテゴリ移動・重複削除等 |
| グループ招待 | +30 | 新メンバーをグループに招待 |
| 実績達成 | +50~ | 各種実績達成時のボーナス |

**レベルアップ**: 100経験値ごとにキャラクターがレベルアップします。

## 運用上の注意点

### データの論理削除
- **shopping_items**: is_deletedフラグによる論理削除を採用
- **メリット**: 削除されたアイテムの統計活用、誤削除からの復旧が可能

### フラグ管理
- **is_active**: 一時的な無効化（復活前提）
- **is_deleted**: 永続的な削除（復旧は特別対応）
- **is_completed**: 状態管理（完了/未完了の切り替え可能）

### パフォーマンス考慮
- **インデックス**: group_id、is_completed、is_deleted、buy_dateに複合インデックスが推奨
- **アーカイブ**: 古い統計データの定期的なアーカイブを検討

この設計により、家族で楽しく使える買い物リスト管理機能と、キャラクター育成によるゲーミフィケーション要素を両立できます。