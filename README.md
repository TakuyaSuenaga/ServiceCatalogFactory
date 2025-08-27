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

## 使用方法

1. **新しいポートフォリオの追加**: `portfolios/` ディレクトリに新しいポートフォリオファイルを作成
2. **プロダクトの追加**: 対応するポートフォリオの `Products/` ディレクトリにプロダクト定義を追加
3. **バージョン管理**: `Versions/` ディレクトリで各プロダクトのバージョンを管理

## 初期設定

このリポジトリには以下の初期設定が含まれています：

- **reinventポートフォリオ**: Cloud Engineeringポートフォリオを含む
- **account-iamプロダクト**: IAMロール管理用のプロダクト（バージョンv1）

## CodePipelineとの連携

このリポジトリはCodePipelineによって監視され、ファイルの変更が検知されると自動的にAWS ServiceCatalogにデプロイされます。

## 要件

- AWS ServiceCatalog Factory
- CodePipeline設定
- 適切なIAM権限
- factory-2019-04-01スキーマ準拠