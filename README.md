# AWS ServiceCatalog Factory Repository

## 概要

このリポジトリは、AWS ServiceCatalog Factory用のポートフォリオとプロダクト定義を管理するためのものです。CodePipelineによって監視され、AWS ServiceCatalogへの自動デプロイメントをサポートします。AMIからEC2インスタンスを作成するデータ収集ミドルウェアプロダクトを管理します。

## リポジトリ構造

```
service-catalog-factory-repo/
├── portfolios/
│   ├── iovpf/
│   │   └── Portfolios/
│   │       └── workload/
│   │           └── Products/
│   │               └── data-collection-middleware/
│   │                   ├── Versions/
│   │                   │   ├── v1/
│   │                   │   │   └── specification.yaml
│   │                   │   └── v2/
│   │                   │       └── specification.yaml
│   │                   └── data-collection-middleware.yaml
│   └── iovpf.yaml
└── README.md
```

## 機能

- **ポートフォリオ管理**: 構造化されたフォルダ階層でServiceCatalogポートフォリオを整理
- **プロダクト分離**: プロダクト定義をポートフォリオ定義から分離し、独立したバージョン管理を実現
- **AMI-EC2統合**: AMIテンプレートからEC2インスタンスを作成するワークロード管理
- **データ収集ミドルウェア**: データ処理ワークロード用のEC2インスタンス自動作成
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
- **ファイル名**: `portfolios/iovpf.yaml`
- **対応ディレクトリ**: `portfolios/iovpf/`
- **重要**: ファイル名（拡張子なし）とディレクトリ名は一致する必要があります

#### 2. ポートフォリオDisplayName
- **iovpf.yaml内**: `DisplayName: "workload"`
- **対応ディレクトリ**: `portfolios/iovpf/Portfolios/workload/`
- **重要**: DisplayNameとディレクトリ名は完全に一致する必要があります

#### 3. プロダクト名
- **プロダクト名**: `data-collection-middleware`
- **ディレクトリ**: `Products/data-collection-middleware/`
- **ファイル名**: `data-collection-middleware.yaml`
- **重要**: プロダクト名、ディレクトリ名、ファイル名（拡張子なし）は一致する必要があります

#### 4. バージョン名
- **バージョン名**: `v1`
- **ディレクトリ**: `Versions/v1/`
- **ファイル名**: `specification.yaml`（固定）

### ファイルの役割

#### portfolios/iovpf.yaml
```yaml
Schema: factory-2019-04-01
Portfolios:
  - DisplayName: "workload"
    Description: "Portfolio for workload management products including EC2 instance creation from AMI templates"
    ProviderName: "IoVPF Operations Team"
    Associations:
      - "arn:aws:iam::${AWS::AccountId}:role/TeamRole"
```
- ポートフォリオの基本情報のみを定義
- プロダクトの詳細は階層構造から自動読み込み

#### Products/data-collection-middleware/data-collection-middleware.yaml
```yaml
Name: "data-collection-middleware"
Description: "Data collection middleware service that creates EC2 instances from pre-configured AMI templates for data processing workloads"
Owner: "iovpf@company.com"
Distributor: "IoVPF Operations Team"
SupportDescription: "Contact IoVPF Operations team for support"
SupportEmail: "iovpf@company.com"
SupportUrl: "https://wiki.company.com/iovpf-operations"
Tags:
  - Key: "ServiceType"
    Value: "DataCollection"
```
- プロダクトのメタデータを定義
- AMIからEC2インスタンス作成用のデータ収集ミドルウェア

#### Versions/v1/specification.yaml
```yaml
Name: "v1"
Description: "Initial version of data collection middleware for EC2 instance creation from AMI templates"
Active: true
Source:
  Provider: "CodeCommit"
  Configuration:
    RepositoryName: "ServiceCatalogFactoryProductTemplate"
    BranchName: "iovpf/Portfolios/workload/Products/data-collection-middleware/Versions/v1"
```
- バージョン固有の設定
- CodeCommitリポジトリからのテンプレート取得設定

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

- **iovpfポートフォリオ**: ワークロード管理ポートフォリオを含む
- **data-collection-middlewareプロダクト**: AMIからEC2インスタンス作成用のデータ収集ミドルウェア（バージョンv1, v2）
- **CodeCommitテンプレート統合**: CodeCommitリポジトリからのテンプレート取得

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

## IAM権限設定

### TeamRoleロール

ポートフォリオにアクセスするためのIAMロールです。

#### 信頼ポリシー（Trust Policy）
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::897729135795:root"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

#### 権限ポリシー（Permissions Policy）
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "servicecatalog:ListProvisioningArtifacts",
                "servicecatalog:DescribeProduct",
                "servicecatalog:DescribeProductView",
                "servicecatalog:DescribeProvisioningArtifact",
                "servicecatalog:DescribeRecord",
                "servicecatalog:ListRecordHistory",
                "servicecatalog:ProvisionProduct",
                "servicecatalog:TerminateProvisionedProduct",
                "servicecatalog:UpdateProvisionedProduct",
                "servicecatalog:SearchProducts",
                "servicecatalog:SearchProvisionedProducts",
                "servicecatalog:ScanProvisionedProducts"
            ],
            "Resource": "*"
        }
    ]
}
```

### ServiceCatalogEndUserAccessポリシー

エンドユーザーがServiceCatalogプロダクトにアクセスするためのポリシーです：

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "servicecatalog:ListProvisioningArtifacts",
                "servicecatalog:DescribeProduct",
                "servicecatalog:DescribeProductView",
                "servicecatalog:DescribeProvisioningArtifact",
                "servicecatalog:DescribeRecord",
                "servicecatalog:ListRecordHistory",
                "servicecatalog:ProvisionProduct",
                "servicecatalog:TerminateProvisionedProduct",
                "servicecatalog:UpdateProvisionedProduct",
                "servicecatalog:SearchProducts",
                "servicecatalog:SearchProvisionedProducts",
                "servicecatalog:ScanProvisionedProducts",
                "servicecatalog:ListPortfolios",
                "servicecatalog:ListAcceptedPortfolioShares",
                "servicecatalog:DescribePortfolio",
                "servicecatalog:ListPortfolioAccess",
                "servicecatalog:ListLaunchPaths"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeImages",
                "ec2:DescribeInstances",
                "ec2:DescribeInstanceTypes",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcs",
                "iam:ListRoles",
                "iam:PassRole"
            ],
            "Resource": "*"
        }
    ]
}
```

## 要件

- AWS ServiceCatalog Factory
- CodePipeline設定
- 適切なIAM権限（上記TeamRoleとServiceCatalogEndUserAccessポリシー）
- factory-2019-04-01スキーマ準拠
- CodeCommitリポジトリ（プロダクトのCloudFormationテンプレート用）