# 実装計画

- [x] 1. リポジトリの基本構造を作成

  - GitHub リポジトリのルートディレクトリ構造を作成
  - portfolios ディレクトリを作成
  - README.md ファイルを作成してリポジトリの目的を説明
  - _要件: 1.1, 1.2_

- [x] 2. 最上位ポートフォリオファイルを作成

  - portfolios/reinvent.yaml ファイルを作成
  - 元の reinvent.yaml の内容（Cloud Engineering ポートフォリオと account-iam プロダクト）を実装
  - factory-2019-04-01 スキーマに準拠した形式で記述
  - _要件: 3.1, 3.2, 4.1_

- [x] 3. 階層化されたポートフォリオディレクトリ構造を作成

  - portfolios/reinvent/ディレクトリを作成
  - portfolios/reinvent/Portfolios/ディレクトリを作成
  - portfolios/reinvent/Portfolios/cloud-engineering-portfolio/ディレクトリを作成
  - AWS 公式ドキュメントのフォルダ構造パターンに従う
  - _要件: 1.3, 4.2_

- [x] 4. プロダクト階層構造を作成

  - portfolios/reinvent/Portfolios/cloud-engineering-portfolio/Products/ディレクトリを作成
  - portfolios/reinvent/Portfolios/cloud-engineering-portfolio/Products/account-iam/ディレクトリを作成
  - プロダクト名に基づいた適切なディレクトリ名を使用
  - _要件: 2.1, 4.1, 4.3_

- [x] 5. プロダクト定義ファイルを作成

  - portfolios/reinvent/Portfolios/cloud-engineering-portfolio/Products/account-iam/account-iam.yaml ファイルを作成
  - プロダクトのメタデータと基本設定を実装
  - reinvent.yaml の内容に基づいたプロダクト情報を記述
  - _要件: 2.1, 3.2, 4.1_

- [x] 6. バージョン管理構造を作成

  - portfolios/reinvent/Portfolios/cloud-engineering-portfolio/Products/account-iam/Versions/ディレクトリを作成
  - portfolios/reinvent/Portfolios/cloud-engineering-portfolio/Products/account-iam/Versions/v1/ディレクトリを作成
  - バージョン別の独立したディレクトリ構造を実装
  - _要件: 2.2, 3.3_

- [x] 7. バージョン仕様ファイルを作成

  - portfolios/reinvent/Portfolios/cloud-engineering-portfolio/Products/account-iam/Versions/v1/specification.yaml ファイルを作成
  - 外部 CodeCommit リポジトリ（account-iam）への参照設定を実装
  - バージョン v1 の仕様とソース設定を記述
  - _要件: 2.3, 3.3_

- [x] 8. ファイル内容の整合性を確認

  - 全ての YAML ファイルが有効な構文であることを確認
  - ポートフォリオとプロダクト間の参照が正確であることを確認
  - factory-2019-04-01 スキーマに準拠していることを確認
  - _要件: 2.4, 4.3_

- [x] 9. リポジトリ構造の最終確認
  - 作成されたディレクトリ構造が AWS 公式ドキュメントのパターンと一致することを確認
  - ファイル名とフォルダ名が reinvent.yaml の内容に基づいていることを確認
  - 将来の拡張性（新しいポートフォリオやプロダクトの追加）が考慮されていることを確認
  - _要件: 1.4, 4.2, 4.3_
