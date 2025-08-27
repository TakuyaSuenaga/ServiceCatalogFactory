# 設計書

## 概要

AWS ServiceCatalog Factory用のGitHubリポジトリ構造を設計します。このリポジトリは、ポートフォリオとプロダクトを分離した階層構造を持ち、CodePipelineによる自動デプロイメントをサポートします。reinventポートフォリオとec2-instanceプロダクトを含む初期構成から始まり、将来的な拡張性を考慮した設計となります。

## アーキテクチャ

### リポジトリ構造

AWS ServiceCatalog Factory公式ドキュメントのフォルダ構造に従います：

```
service-catalog-factory-repo/
├── portfolios/
│   ├── reinvent/
│   │   └── Portfolios/
│   │       └── cloud-engineering-portfolio/
│   │           └── Products/
│   │               └── account-iam/
│   │                   ├── Versions/
│   │                   │   └── v1/
│   │                   │       └── specification.yaml
│   │                   └── account-iam.yaml
│   └── reinvent.yaml
└── README.md
```

この構造では：
- `reinvent.yaml`: ポートフォリオの最上位定義ファイル
- `Portfolios/cloud-engineering-portfolio/`: 個別ポートフォリオのディレクトリ
- `Products/account-iam/`: プロダクト固有のディレクトリ
- `Versions/v1/specification.yaml`: バージョン固有の仕様ファイル
- `account-iam.yaml`: プロダクトの定義ファイル

### 設計原則

1. **分離の原則**: ポートフォリオとプロダクトの定義を分離
2. **バージョン管理**: プロダクトのバージョンを独立したディレクトリで管理
3. **スケーラビリティ**: 新しいポートフォリオやプロダクトの追加が容易
4. **AWS標準準拠**: AWS ServiceCatalog Factoryのベストプラクティスに従う

## コンポーネントとインターフェース

### ポートフォリオコンポーネント

#### 最上位ポートフォリオファイル
**場所**: `portfolios/{portfolio-name}.yaml`

**責任**:
- ポートフォリオの最上位定義
- 元のreinvent.yamlの内容を保持

#### 階層化されたポートフォリオ構造
**場所**: `portfolios/{portfolio-name}/Portfolios/{portfolio-detail}/`

**責任**:
- 詳細なポートフォリオ設定
- プロダクトの階層管理
- 将来の拡張性確保

#### プロダクト定義ファイル
**場所**: `portfolios/{portfolio-name}/Portfolios/{portfolio-detail}/Products/{product-name}/{product-name}.yaml`

#### バージョン仕様ファイル
**場所**: `portfolios/{portfolio-name}/Portfolios/{portfolio-detail}/Products/{product-name}/Versions/{version}/specification.yaml`

**責任**:
- バージョン固有の設定
- 外部CodeCommitリポジトリへの参照

### プロダクトコンポーネント

**場所**: 別のCodeCommitリポジトリ（例：account-iam）

**責任**:
- プロダクトのCloudFormationテンプレート管理
- プロダクト固有のリソース定義

**ファイル構成**:
- CloudFormationテンプレートファイル（.yaml または .json）
- 必要に応じてパラメータファイルやその他のリソース

**ServiceCatalog Factoryリポジトリでの参照方法**:
ポートフォリオファイル内でSourceセクションを使用して外部リポジトリを参照

**注意**: プロダクトの実際のCloudFormationテンプレートは、別のCodeCommitリポジトリ（この場合は"account-iam"）に配置され、ServiceCatalog Factoryリポジトリからは参照のみを行います。

**ポートフォリオファイルの構造**:
```yaml
Schema: factory-2019-04-01
Portfolios:
  - DisplayName: "ポートフォリオ表示名"
    Description: "ポートフォリオの説明"
    ProviderName: "プロバイダー名"
    Associations:
      - "arn:aws:iam::${AWS::AccountId}:role/RoleName"
    Products:
      - Name: "プロダクト名"
        Owner: "所有者メールアドレス"
        Description: "プロダクトの説明"
        Distributor: "配布者"
        SupportDescription: "サポート情報"
        SupportEmail: "サポートメールアドレス"
        SupportUrl: "サポートURL"
        Tags:
          - Key: "product-type"
            Value: "タグ値"
        Versions:
          - Name: "v1"
            Description: "バージョンの説明"
            Active: true
            Source:
              Provider: "CodeCommit"
              Configuration:
                RepositoryName: "プロダクト専用リポジトリ名"
                BranchName: "ブランチ名"
```

## データモデル

### Reinventポートフォリオの具体的な設定

**ポートフォリオ名**: reinvent
**プロダクト**: account-iam
**バージョン**: v1

### 実際のreinvent.yamlの内容

元のreinvent.yamlファイルの最終形は以下の構造になります：

```yaml
Schema: factory-2019-04-01
Portfolios:
  - DisplayName: "Cloud Engineering"
    Description: "Portfolio containing products for cloud engineering"
    ProviderName: "Cloud Engineering"
    Associations:
      - "arn:aws:iam::${AWS::AccountId}:role/TeamRole"
    Products:
      - Name: "account-iam"
        Owner: "central-it@customer.com"
        Description: "The iam roles needed for you to do your jobs"
        Distributor: "central-it-team"
        SupportDescription: "Contact us on Chime for help #central-it-team"
        SupportEmail: "central-it-team@customer.com"
        SupportUrl: "https://wiki.customer.com/central-it-team/self-service/account-iam"
        Tags:
          - Key: "product-type"
            Value: "iam"
        Versions:
          - Name: "v1"
            Description: "The iam roles needed for you to do your jobs"
            Active: true
            Source:
              Provider: "CodeCommit"
              Configuration:
                RepositoryName: "account-iam"
                BranchName: "v1"
```

### ファイルマッピング

1. **portfolios/reinvent.yaml**
   - 最上位のポートフォリオ定義ファイル
   - 元のreinvent.yamlの内容をそのまま配置

2. **portfolios/reinvent/Portfolios/cloud-engineering-portfolio/**
   - Cloud Engineeringポートフォリオの詳細設定（将来の拡張用）

3. **portfolios/reinvent/Portfolios/cloud-engineering-portfolio/Products/account-iam/account-iam.yaml**
   - account-iamプロダクトの定義ファイル

4. **portfolios/reinvent/Portfolios/cloud-engineering-portfolio/Products/account-iam/Versions/v1/specification.yaml**
   - バージョンv1の仕様ファイル
   - 外部CodeCommitリポジトリへの参照設定

## エラーハンドリング

### ファイル構造エラー

1. **欠落ファイル**: 必須ファイルが存在しない場合のエラーメッセージ
2. **無効なYAML**: YAML構文エラーの検出と報告
3. **参照エラー**: 存在しないプロダクトやバージョンへの参照

### CodePipelineエラー

1. **デプロイメントエラー**: CloudFormationスタック作成失敗時の処理
2. **権限エラー**: IAMロールや権限不足時の対応
3. **リソース競合**: 既存リソースとの競合時の処理

## CodePipelineとの連携

### 監視対象

CodePipelineは以下のファイル変更を監視します：

1. **portfolios/*.yaml**: 最上位ポートフォリオ定義ファイル
2. **portfolios/*/Portfolios/**: 階層化されたポートフォリオ構造
3. **Products/**: プロダクト定義とバージョン仕様

### 自動デプロイメント

ファイルの変更が検知されると、CodePipelineが以下を実行します：

1. **変更検知**: GitHubリポジトリの更新を検知
2. **ビルド**: ServiceCatalog Factoryによる設定の処理
3. **デプロイ**: AWS ServiceCatalogへのポートフォリオとプロダクトの反映

### ファイル形式要件

- **YAML構文**: 全てのファイルは有効なYAML形式である必要があります
- **Schema準拠**: factory-2019-04-01スキーマに準拠する必要があります
- **参照整合性**: 外部CodeCommitリポジトリへの参照が正確である必要があります