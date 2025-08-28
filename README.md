# AWS ServiceCatalog Factory Repository

## 概要

このリポジトリは、AWS ServiceCatalog Factory用のポートフォリオとプロダクト定義を管理するためのものです。CodePipelineによって監視され、AWS ServiceCatalogへの自動デプロイメントをサポートします。

## リポジトリ構造

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

## 機能

- **ポートフォリオ管理**: 構造化されたフォルダ階層でServiceCatalogポートフォリオを整理
- **プロダクト分離**: プロダクト定義をポートフォリオ定義から分離し、独立したバージョン管理を実現
- **スケーラビリティ**: 新しいポートフォリオやプロダクトの追加が容易な構造
- **AWS標準準拠**: AWS ServiceCatalog Factoryのベストプラクティスに従った設計

## 階層構造と命名規則

### ファイル構造の関係

AWS ServiceCatalog Factoryは、以下の命名規則に基づいて階層構造を自動的に検出します：

```
portfolios/{portfolio-file-name}.yaml
└── portfolios/{portfolio-file-name}/
    └── Portfolios/{DisplayName}/
        └── Products/{product-name}/
            ├── {product-name}.yaml
            └── Versions/
                └── {version-name}/
                    └── specification.yaml
```

### 命名規則の詳細

#### 1. ポートフォリオファイル
- **ファイル名**: `portfolios/reinvent.yaml`
- **対応ディレクトリ**: `portfolios/reinvent/`
- **重要**: ファイル名（拡張子なし）とディレクトリ名は一致する必要があります

#### 2. ポートフォリオDisplayName
- **reinvent.yaml内**: `DisplayName: "cloud-engineering-portfolio"`
- **対応ディレクトリ**: `portfolios/reinvent/Portfolios/cloud-engineering-portfolio/`
- **重要**: DisplayNameとディレクトリ名は完全に一致する必要があります

#### 3. プロダクト名
- **プロダクト名**: `account-iam`
- **ディレクトリ**: `Products/account-iam/`
- **ファイル名**: `account-iam.yaml`
- **重要**: プロダクト名、ディレクトリ名、ファイル名（拡張子なし）は一致する必要があります

#### 4. バージョン名
- **バージョン名**: `v1`
- **ディレクトリ**: `Versions/v1/`
- **ファイル名**: `specification.yaml`（固定）

### ファイルの役割

#### portfolios/reinvent.yaml
```yaml
Schema: factory-2019-04-01
Portfolios:
  - DisplayName: "cloud-engineering-portfolio"
    Description: "Portfolio containing products for cloud engineering"
    ProviderName: "Cloud Engineering"
    Associations:
      - "arn:aws:iam::${AWS::AccountId}:role/TeamRole"
```
- ポートフォリオの基本情報のみを定義
- プロダクトの詳細は階層構造から自動読み込み

#### Products/account-iam/account-iam.yaml
```yaml
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
```
- プロダクトのメタデータを定義
- バージョンの参照のみ（詳細は別ファイル）

#### Versions/v1/specification.yaml
```yaml
Name: "v1"
Description: "The iam roles needed for you to do your jobs"
Active: true
Source:
  Provider: "CodeCommit"
  Configuration:
    RepositoryName: "account-iam"
    BranchName: "v1"
```
- バージョン固有の設定
- ソースリポジトリの参照

## 使用方法

### 新しいポートフォリオの追加

1. `portfolios/` ディレクトリに新しいポートフォリオファイルを作成（例：`new-portfolio.yaml`）
2. 対応するディレクトリ構造を作成：`portfolios/new-portfolio/Portfolios/{DisplayName}/`

### 新しいプロダクトの追加

1. 既存のポートフォリオに追加する場合：
   ```
   portfolios/{portfolio-name}/Portfolios/{DisplayName}/Products/{product-name}/
   ├── {product-name}.yaml
   └── Versions/
       └── v1/
           └── specification.yaml
   ```

2. ファイルを作成後、CodePipelineが自動的に検出・デプロイ

### 新しいバージョンの追加

1. 既存プロダクトに新バージョンを追加：
   ```
   Products/{product-name}/Versions/
   ├── v1/
   │   └── specification.yaml
   └── v2/  ← 新規追加
       └── specification.yaml
   ```

2. `{product-name}.yaml`のVersionsセクションに新バージョンを追加

## 初期設定

このリポジトリには以下の初期設定が含まれています：

- **reinventポートフォリオ**: Cloud Engineeringポートフォリオを含む
- **account-iamプロダクト**: IAMロール管理用のプロダクト（バージョンv1）

## CodePipelineとの連携

このリポジトリはCodePipelineによって監視され、ファイルの変更が検知されると自動的にAWS ServiceCatalogにデプロイされます。

## 重要な注意事項

### 命名規則の厳守
- **ファイル名とディレクトリ名の一致**: 自動検出が正常に動作するために必須
- **DisplayNameとディレクトリ名の一致**: ポートフォリオが正しく認識されるために必須
- **プロダクト名の一致**: プロダクト名、ディレクトリ名、ファイル名は完全に一致させる

### ファイル形式
- **YAML構文**: 全てのファイルは有効なYAML形式である必要があります
- **Schema準拠**: factory-2019-04-01スキーマに準拠する必要があります
- **必須フィールド**: 各ファイルで必要なフィールドを漏れなく記述する

### トラブルシューティング

#### プロダクトが表示されない場合
1. ディレクトリ名とファイル名の一致を確認
2. DisplayNameとディレクトリ名の一致を確認
3. YAML構文エラーがないか確認

#### バージョン名が表示されない場合
1. `specification.yaml`に`Name`フィールドが含まれているか確認
2. `{product-name}.yaml`のVersionsセクションにバージョンが定義されているか確認

## 要件

- AWS ServiceCatalog Factory
- CodePipeline設定
- 適切なIAM権限（TeamRoleなど）
- factory-2019-04-01スキーマ準拠
- 外部CodeCommitリポジトリ（プロダクトのCloudFormationテンプレート用）