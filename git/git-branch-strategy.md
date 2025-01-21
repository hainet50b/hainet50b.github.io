# Gitブランチ戦略（GitHub Flow / Git Flow）

## GitHub Flow
参考：[GitHub Flow - Scott Chacon](https://scottchacon.com/2011/08/31/github-flow/){:target="_blank"} ([日本語訳](https://gist.github.com/repeatedly/4554765){:target="_blank"})

masterブランチが自動的にデプロイされることを前提に、デプロイを中心としたワークフロー。  
リリースという概念はなく、一日に何十回とデプロイを実施する場合に適している。

ブランチの役割は以下の通り。
- master：常にデプロイできるブランチ。テストが失敗するコードを含めてはならない。
- topic：機能開発を行うブランチ。機能開発もバグの修正も同様の手順を踏む。

GitHub Flowは早期にPRを作成して"PRを育てる"ことを提唱した点が秀逸。  
差分閲覧機能やコメント機能を活用してPRを開発の中心に据える。

PRを育てるにあたって注意する点は以下の通り。
- PRのサイズは可能な限り小さくする。バグが入る余地が減り、レビュアーも楽になる。
- PRへのフィードバックを適量に抑える。長期間クローズされない場合は以下の問題を抱えている可能性が高い。
  - コミュニケーション不足（対話が必要）
  - 開発者の能力不足（教育が必要）
- PRを溜めない。チームはPRを作ることより、PRを消化することを優先する。

全体を通して、シンプルなワークフローであることに価値を置いている。

## Git Flow
参考：[A successful Git branching model >> nvie.com](https://nvie.com/posts/a-successful-git-branching-model/){:target="_blank"}

複数の機能開発の合流や開発状況と切り離したバグ修正を可能とするリリースを中心としたワークフロー。  
数週間や数ヶ月に一度の頻度でデプロイを実施する場合に適している。

![git-model@2x.png](https://nvie.com/img/git-model@2x.png)
出典：https://nvie.com/posts/a-successful-git-branching-model/

ブランチの役割は以下の通り。
- master：常に正常に動作するブランチ。デプロイされているとは限らない。
- develop：開発の中心となるブランチ。
- feature/*：developから作成して、機能開発を行うブランチ。
- release/*：developから作成して、リリース前の最終調整を行うブランチ。
- hotfix/*：masterから作成して、バグ修正を行うブランチ。

featureとdevelopの間でPRを作成して、GitHub FlowのようにPRを育てていく。  
releaseとhotfixのマージ先が複数になるなどやや複雑なため、一定の学習が求められる。
