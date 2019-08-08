# Schematics

Schematicは複雑なロジックをサポートするテンプレートベースのコードジェネレーターです。
コードを生成または変更することによって、ソフトウェアプロジェクトを変換するための一連の命令です。  
Schematicsは[コレクション](guide/glossary#collection)にパッケージ化され、npmとともにインストールされます。  

Schematicコレクションはソフトウェアプロジェクトを作成、変更、および保守するための強力なツールになりますが、Angularプロジェクトをカスタマイズして自分の組織の特定のニーズに合わせるのに特に有用です。  
例えば、事前に定義されたテンプレートまたはレイアウトを使って、一般的に使用されるUIパターンまたは特定のコンポーネントを生成するために、Schematicsを使用できます。  
Schematicsを使用して、アーキテクチャのルールや約束を適用することで、プロジェクトの一貫性と相互運用性を実現できます。  

## Angular CLI の Schematics

SchematicsはAngularのエコシステムの一部です。[Angular CLI](guide/glossary#cli)はSchematicsを使用してWebアプリプロジェクトに変換を適用します。  
Schematicsを変更することで、依存関係の破壊的変更を修正するためにコードを更新したり、既存のプロジェクトに新しい構成オプションまたはフレームワークを追加したりするなどの新しいSchematicsを定義することができます。  

`@schematics/angular`コレクションに含まれるschematicsは、`ng generate`や`ng add`コマンドによってデフォルトで実行されます。   
パッケージには、`ng generate`サブコマンド（`ng generate component`や`ng generate service`）などCLIで使用可能なオプションを構成する名前付きの schematics が含まれています。  
`ng generate`のサブコマンドは、対応するschematicの略記です。 次のような形式を使用することで、生成する特定のschematic（またはschematicsのコレクション）を指定できます。  

<code-example language="bash" linenums="false">
ng generate my-schematic-collection:my-schematic-name
</code-example>

&mdash;or&mdash;

<code-example language="bash" linenums="false">
ng generate my-schematic-name --collection collection-name
</code-example>

### CLI schematicsの構成

schematicに関連付けられたJSONスキーマは、コマンドとサブコマンドで使用可能なオプションをAngular CLIに通知し、デフォルトを決定します。  
これらのデフォルトは、コマンドラインのオプションに異なる値を指定することでオーバーライドできます。  
ワークスペースの生成オプションのデフォルトを変更する方法については、[ワークスペースの構成](guide/workspace-config)を参照してください。  

CLIがプロジェクトやその一部を生成するために使用するデフォルトのschematicsのJSONスキーマは、パッケージ[`@schematics/angular`](https://raw.githubusercontent.com/angular/angular-cli/v7.0.0/packages/schematics/angular/application/schema.json)にまとまっています。
スキーマは、 `--help`の出力に示されているように、`ng generate`サブコマンドごとにCLIで利用可能なオプションを記述します。

## ライブラリのためのschematics開発

ライブラリ開発者は、カスタムschematicsの独自のコレクションを作成して、ライブラリをAngular CLIと統合できます。

* *add schematic*によって、開発者は `ng add`を使用して、Angularワークスペースにライブラリをインストールできます。

* *Generation schematics*は、ライブラリで定義されているプロジェクトの変更、設定とスクリプト、およびscaffoldアーチファクトの追加方法を`ng generate`サブコマンドに伝えることができます。
* *Generation schematics* can tell the `ng generate` subcommands how to modify projects, add configurations and scripts, and scaffold  artifacts that are defined in your library.

* *update schematic*は、`ng update`コマンドにライブラリの依存関係を更新し、新しいバージョンをリリースする際の破壊的変更を調整する方法を指示できます。

これらの使い方と作成方法の詳細については、以下を参照してください。
* [Schematicsの作成](guide/schematics-authoring)
* [ライブラリのためのSchematics](guide/schematics-for-libraries)

### add schematics

add schematicsには通常ライブラリが付属しているため、`ng add`を使用してライブラリを既存のプロジェクトに追加できます。  
`add`コマンドはパッケージマネージャーを使用して新しい依存関係をダウンロードし、schematicとして実装されているインストールスクリプトを呼び出します。

たとえば、[`@angular/material`]（https://material.angular.io/guide/schematics）schematicは、Angular Materialとテーマをインストールして設定し、`ng generate`で作成できる新しいスターターコンポーネントを登録するように`add`コマンドに指示します。  
これを例として見て、独自のadd schematicを作ることができます。

パートナーおよびサードパーティのライブラリも、add schematicsとともにAngular CLIをサポートしています。
たとえば、`@ng-bootstrap/schematics`は[ng-bootstrap](https://ng-bootstrap.github.io/)をアプリに追加し、`@clr/angular` は[Clarity from VMWare]をインストールして設定します。

add schematicは構成を変更してプロジェクトを更新したり、（ポリフィルなどの）依存関係を追加したり、パッケージ固有の初期化コードをscaffoldしたりできます。  
たとえば、`@angular/pwa`schematicはアプリマニフェストとサービスワーカーを追加することでアプリケーションをPWAに変換し、`@angular/elements`schematicは `document-register-element.js`ポリフィルとAngular Elementsの依存関係を追加します。

### Generation schematics

Generation schematicsは、`ng generate`コマンドの指示です。  
ドキュメント化されたサブコマンドはデフォルトのAngular generation schematicsを使用しますが、ライブラリーで定義されたアーティファクトを生成するために、（サブコマンドの代わりに）異なるschematicを指定できます。

たとえば、Angular Materialは、定義するUIコンポーネントのgeneration schematicsを提供します。  
次のコマンドは、これらのschematicsのひとつを使用して、ソートおよびページネーション用のデータソースで事前設定されたAngular Materialの`<mat-table>`をレンダリングします。

<code-example language="bash" linenums="false">
ng generate @angular/material:table <component-name>
</code-example>

### Update schematics

`ng update`コマンドを使用して、ワークスペースのライブラリの依存関係を更新できます。オプションを指定しないか、ヘルプオプションを使用すると、コマンドはワークスペースを調べ、更新するライブラリを提案します。

<code-example language="bash" linenums="false">
ng update
    We analyzed your package.json, there are some packages to update:

      Name                               Version                  Command to update
     --------------------------------------------------------------------------------
      @angular/cdk                       7.2.2 -> 7.3.1           ng update @angular/cdk
      @angular/cli                       7.2.3 -> 7.3.0           ng update @angular/cli
      @angular/core                      7.2.2 -> 7.2.3           ng update @angular/core
      @angular/material                  7.2.2 -> 7.3.1           ng update @angular/material
      rxjs                               6.3.3 -> 6.4.0           ng update rxjs


    There might be additional packages that are outdated.
    Run "ng update --all" to try to update all at the same time.
</code-example>

コマンドに更新するライブラリのセット（または `--all`フラグ）を渡すと、それらのライブラリ、それらのpeer依存関係、およびそれらに依存するpeer依存関係が更新されます。

<div class="alert is-helpful">

矛盾がある場合（たとえば、peer依存関係を単純な[semver](https://semver.io/)の範囲で照合できない場合）、コマンドはエラーを生成し、ワークスペース内の何も変更しません。

デフォルトでは、すべての依存関係の更新を強制しないことをお勧めします。 最初に特定の依存関係を更新してみてください。

「ng update」コマンドの機能の詳細については、[Update Command](https://github.com/angular/angular-cli/blob/master/docs/specifications/update.md)を参照してください。

</div>

潜在的な破壊的変更をもたらすライブラリの新しいバージョンを作成する場合、*update schematic*を提供して、`ng update`コマンドによって、更新されているプロジェクトのそのような変更を自動的に解決できるようにします。

たとえば、Angular Materialライブラリを更新するとします。

<code-example language="bash" linenums="false">
ng update @angular/material
</code-example>

このコマンドは、ワークスペースの`package.json`内の`@angular/material`とその依存関係`@angular/cdk`の両方を更新します。
いずれかのパッケージに、既存のバージョンから新しいバージョンへの移行をカバーするupdate schematicが含まれている場合、コマンドはワークスペースでそのschematicを実行します。
