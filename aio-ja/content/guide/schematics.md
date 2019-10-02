# Schematics

Schematicは複雑なロジックをサポートするテンプレートベースのコードジェネレーターです。
コードを生成または変更することによってソフトウェアプロジェクトを変換するための一連の命令です。  
Schematicsは[コレクション](guide/glossary#collection)にパッケージ化され、npmでインストールされます。  

Schematicコレクションはソフトウェアプロジェクトを作成、変更、および保守するための強力なツールになりますが、特にAngularプロジェクトをカスタマイズして自分の組織の特定のニーズに合わせるのに有用です。  
たとえば、事前に定義されたテンプレートやレイアウトを使って、一般的に使われるUIパターンまたは特定のコンポーネントを生成するためにSchematicsを使うことができます。  
Schematicsを使用してアーキテクチャのルールや規約を適用することで、プロジェクトの一貫性と相互運用性を実現できます。  

## Angular CLI の Schematics

SchematicsはAngularのエコシステムの一部です。[Angular CLI](guide/glossary#cli)はSchematicsを使用してWebアプリプロジェクトを変換します。  
Schematicsを変更することで、依存関係の破壊的変更を修正するためにコードを更新したり、既存のプロジェクトに新しい構成オプションまたはフレームワークを追加したりする新しいSchematicsを定義することができます。  

`@schematics/angular`コレクションに含まれるSchematicsは、`ng generate`や`ng add`コマンドによってデフォルトで実行されます。   
パッケージには、`ng generate`サブコマンド（`ng generate component`や`ng generate service`）などCLIで使用可能なオプションを構成する名前付きのSchematicsが含まれています。  
`ng generate`のサブコマンドは、対応するSchematicの簡略表記です。次の形式を使用することで、生成する特定のSchematic（またはSchematicsコレクション）を指定できます。  

<code-example language="bash" linenums="false">
ng generate my-schematic-collection:my-schematic-name
</code-example>

&mdash;または&mdash;

<code-example language="bash" linenums="false">
ng generate my-schematic-name --collection collection-name
</code-example>

### CLI Schematicsの構成

Schematicに関連付けられたJSONスキーマは、コマンドとサブコマンドで使用可能なオプションをAngular CLIに通知し、デフォルトを決定します。  
これらのデフォルトは、コマンドラインのオプションに異なる値を指定することでオーバーライドすることができます。  
ワークスペースの生成オプションのデフォルトを変更する方法については、[ワークスペースの構成](guide/workspace-config)を参照してください。  

CLIがプロジェクトやその一部を生成するために使用するデフォルトのSchematicsのJSONスキーマは、パッケージ[`@schematics/angular`](https://raw.githubusercontent.com/angular/angular-cli/v7.0.0/packages/schematics/angular/application/schema.json)にまとまっています。
スキーマは、 `--help`の出力に示されているように、`ng generate`サブコマンドごとにCLIで利用可能なオプションを記述します。

## ライブラリのためのSchematics開発

ライブラリ開発者は、カスタムSchematicsの独自のコレクションを作成して、ライブラリをAngular CLIと統合することができます。

* *schematicの追加*では、開発者は `ng add`を使用して、Angularワークスペースにライブラリをインストールできます。

* *Schematicsの生成*では、プロジェクトの変更、設定とスクリプトの追加、およびライブラリで定義されているアーティファクトの雛形の作り方を`ng generate`サブコマンドで指定することができます。

* *Schematicの更新*では、`ng update`コマンドによって、ライブラリの依存関係を更新し、新しいバージョンをリリースする際の破壊的変更を調整することができます。

これらの使い方と作成方法の詳細については、以下を参照してください。
* [Schematicsの作成](guide/schematics-authoring)
* [ライブラリのためのSchematics](guide/schematics-for-libraries)

### Schematicsの追加

Schematicsの追加機能は通常ライブラリに付属しているため、`ng add`を使用してライブラリを既存のプロジェクトに追加できます。  
`add`コマンドはパッケージマネージャーを使用して新しい依存関係をダウンロードし、Schematicとして実装されているインストールスクリプトを呼び出します。

たとえば、[`@angular/material`]（https://material.angular.io/guide/schematics）schematicは、Angular Materialとテーマをインストールして設定し、`ng generate`で作成できる新しいスターターコンポーネントを登録するように`add`コマンドに指示します。  
これを一つの例として、独自のSchematics追加機能を作ることができます。

パートナーおよびサードパーティのライブラリも、Schematics追加機能とともにAngular CLIをサポートしています。
たとえば、`@ng-bootstrap/schematics`は[ng-bootstrap](https://ng-bootstrap.github.io/)をアプリに追加し、`@clr/angular` は[Clarity from VMWare]をインストールして設定します。

Schematicsの追加によって、構成を変更してプロジェクトを更新したり、（ポリフィルなどの）依存関係を追加したり、パッケージ固有の初期コードの雛形を生成することができます。  
たとえば、`@angular/pwa` schematicはアプリマニフェストとサービスワーカーを追加することでアプリケーションをPWAに変換し、`@angular/elements` schematicは `document-register-element.js`ポリフィルとAngular Elementsの依存関係を追加します。

### Schematicsの生成

Schematicsの生成機能は、`ng generate`コマンドの命令です。  
ドキュメント化されたサブコマンドはデフォルトのAngular generation schematicsを使用しますが、ライブラリで定義されたアーティファクトを生成するために、（サブコマンドの代わりに）異なるSchematicを指定できます。

たとえば、Angular Materialは、定義するUIコンポーネントのSchematicsの生成機能を提供します。  
次のコマンドは、これらのSchematicsのひとつを使用して、ソートおよびページネーション用のデータソースで事前設定されたAngular Materialの`<mat-table>`をレンダリングします。

<code-example language="bash" linenums="false">
ng generate @angular/material:table <component-name>
</code-example>

### Schematicの更新

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

コマンドに更新するライブラリのセット（または `--all`フラグ）を渡すと、それらのライブラリ、それらのピア依存関係、およびそれらに依存するピア依存関係が更新されます。

<div class="alert is-helpful">

矛盾がある場合（たとえば、ピア依存関係を単純な[semver](https://semver.io/)の範囲で照合できない場合）、コマンドはエラーを生成し、ワークスペース内に対し何も変更しません。

デフォルトでは、すべての依存関係を強制的に更新ないことをお勧めします。 最初に特定の依存関係を更新してみてください。

「ng update」コマンドの機能の詳細については、[Update Command](https://github.com/angular/angular-cli/blob/master/docs/specifications/update.md)を参照してください。

</div>

潜在的な破壊的変更をもたらすライブラリの新しいバージョンを作成する場合、*Schematicの更新機能*を提供して、`ng update`コマンドによって、更新されているプロジェクトのそのような変更を自動的に解決できるようにします。

たとえば、Angular Materialライブラリを更新するとします。

<code-example language="bash" linenums="false">
ng update @angular/material
</code-example>

このコマンドは、ワークスペースの`package.json`内の`@angular/material`とその依存関係`@angular/cdk`の両方を更新します。
いずれかのパッケージに、既存のバージョンから新しいバージョンへの移行をカバーするSchematicの更新機能が含まれている場合、コマンドはワークスペースでそのSchematicを実行します。
