# Angularのフォームのイントロダクション

ユーザー入力をフォームで処理することは、多くの一般的なアプリケーションで不可欠なものです。アプリケーションはフォームを使用して、ユーザーがログインしたり、プロフィールを更新したり、機密情報を入力したり、その他の多くのデータ入力タスクを実行できるようにします。

Angularは、フォームを通じてユーザー入力を処理するための2つの異なるアプローチを提供します。リアクティブ型とテンプレート駆動型です。どちらもビューからユーザー入力イベントを取得し、ユーザー入力をバリデーションし、更新するフォームモデルとデータモデルを作成し、変更を監視する方法を提供します。

リアクティブフォームとテンプレート駆動フォームでは、フォームデータの処理方法と管理方法が異なります。それぞれには異なる利点があります。

**一般に:**

* **リアクティブフォーム** はより堅牢です。よりスケーラブルで、再利用しやすく、そしてテストがしやすいです。フォームがアプリケーションの重要なパーツである場合、またはアプリケーションの構築にリアクティブパターンをすでに使用している場合は、リアクティブフォームを使用してください。
* **テンプレート駆動フォーム** は、メーリングリストの申し込みフォームなどの単純なフォームをアプリに追加するのに役立ちます。アプリに追加するのは簡単ですが、リアクティブフォームほどスケーラビリティはありません。テンプレートでのみ管理できるとても基本的なフォーム要件とロジックをもつような場合は、テンプレート駆動フォームを使用してください。

このガイドでは、どちらの種類のフォームが自分の状況にもっとも適しているかを判断するのに役立つ情報を提供します。両方のアプローチで使用される共通の構成要素を紹介します。また、2つのアプローチの主な違いをまとめ、セットアップ、データフロー、およびテストの観点から見た違いを説明します。

<div class="alert is-helpful">

**Note:** 各種類のフォームに関する完全な情報については、[リアクティブフォーム](guide/reactive-forms)と[テンプレート駆動フォーム](guide/forms)を参照してください。

</div>

## 主な違い

次の表は、リアクティブフォームとテンプレート駆動フォームの主な違いをまとめたものです。

<style>
  table {width: 100%};
  td, th {vertical-align: top};
</style>

||リアクティブ|テンプレート駆動|
|--- |--- |--- |
|セットアップ(フォームモデル)|より明示的です。コンポーネントクラス内で作成されます|あまり明示的ではありません。ディレクティブによって作成されます|
|データモデル|構造化されています|構造化されていません|
|プレディクタビリティ(予測可能性)|同期的|非同期的|
|フォームバリデーション|関数|ディレクティブ|
|ミュータビリティ(可変性)|イミュータブル|ミュータブル|
|スケーラビリティ(拡張性)|低レベルAPIアクセス|API上で抽象化されます|

## 共通の基盤

リアクティブとテンプレート駆動の両方のフォームは、基礎となる構成要素を共有しています。


* `FormControl`は個々のフォームコントロールの値とバリデーションステータスを監視します。

* `FormGroup`はフォームコントロールのコレクションに対して同じ値とステータスを監視します。

* `FormArray`はフォームコントロールの配列に対して同じ値とステータスを監視します。

* `ControlValueAccessor`はAngularの`FormControl`インスタンスとネイティブのDOM要素との間の橋渡しをします。

リアクティブフォームとフォーム駆動フォームで、どのようにこれらのコントロールのインスタンスが作成されて管理されるかの説明については、次の[フォームモデルのセットアップ](#setup-the-form-model)セクションを参照してください。さらなる詳細はこのガイドの[データフローセクション](#data-flow-in-forms)を参照してください。

{@a setup-the-form-model}

## フォームモデルのセットアップ

リアクティブフォームとテンプレート駆動フォームはどちらもフォームモデルを使用して、Angularフォームとフォームのinput要素間の値の変化を監視します。次の例では、フォームモデルの定義と作成方法を説明しています。

### リアクティブフォームでのセットアップ

リアクティブフォームを使用して実装された、単一のコントロールの入力フィールドをもつコンポーネントは次のようになります。

<code-example path="forms-overview/src/app/reactive/favorite-color/favorite-color.component.ts">
</code-example>

特定の時点のform要素の値と状態は、真のソースが提供します。リアクティブフォームでは、フォームモデルが真のソースになります。上記の例では、フォームモデルは`FormControl`インスタンスです。

<div class="lightbox">
  <img src="generated/images/guide/forms-overview/key-diff-reactive-forms.png" alt="Reactive forms key differences">
</div>

リアクティブフォームでは、フォームモデルはコンポーネントクラスで明示的に定義されています。次に、リアクティブフォームディレクティブ(この場合は`FormControlDirective`)は、値アクセサ(`ControlValueAccessor`インスタンス)を使って既存の`FormControl`インスタンスをビュー内の特定のform要素にリンクします。

### テンプレート駆動フォームでのセットアップ

テンプレート駆動フォームを使用して実装された、同じような単一のコントロールの入力フィールドをもつコンポーネントは次のようになります。

<code-example path="forms-overview/src/app/template/favorite-color/favorite-color.component.ts">
</code-example>

テンプレート駆動フォームでは、真のソースはテンプレートです。

<div class="lightbox">
  <img src="generated/images/guide/forms-overview/key-diff-td-forms.png" alt="Template-driven forms key differences">
</div>

フォームモデルの抽象化により、構造化するよりも単純さが増します。テンプレート駆動のフォームディレクティブ`NgModel`が、与えられたform要素の`FormControl`インスタンスを作成し管理する責務を持ちます。明示的ではありませんが、フォームモデルを直接制御する必要がなくなります。

{@a data-flow-in-forms}

## フォーム内のデータフロー

Angularでフォームを作成するときは、フレームワークがユーザー、またはプログラムによる変更からのデータフローをどのように処理するかを理解することが重要です。リアクティブフォームとテンプレート駆動フォームは、フォーム入力を処理するときに2つの異なる戦略に従います。次のデータフローの例は、さきほど説明したお気に入りの色の入力フィールドの例から始まり、そのあとにお気に入りの色の変更がリアクティブフォームとテンプレート駆動フォームでどのように処理されるかを比較します。

### リアクティブフォームでのデータフロー

さきほど説明したように、リアクティブフォームでは、ビュー内の各form要素はフォームモデル(`FormControl`インスタンス)に直接リンクされています。ビューからモデルへの更新とモデルからビューへの更新は同期的であり、レンダリングされたUIには依存しません。次の図では、同じお気に入りの色の例を使用して、入力フィールドの値がビューから変更されたときと、モデルから変更されたときのデータフローを説明しています。

<div class="lightbox">
  <img src="generated/images/guide/forms-overview/dataflow-reactive-forms-vtm.png" alt="Reactive forms data flow - view to model" width="100%">
</div>

次の手順は、ビューからモデルへのデータフローの概要です。

1. ユーザーがinput要素に値を入力します。このケースでは、お気に入りの色は*Blue*です。
1. フォームのinput要素は最新の値で"input"イベントを発行します。
1. フォームのinput要素上のイベントをリッスンしているコントロール値アクセサは、新しい値をただちに`FormControl`インスタンスに渡します。
1. `FormControl`インスタンスは`valueChanges`オブジェクトを通して新しい値を発行します。
1. `valueChanges`Observableのサブスクライバーが新しい値を受け取ります。

<div class="lightbox">
  <img src="generated/images/guide/forms-overview/dataflow-reactive-forms-mtv.png" alt="Reactive forms data flow - model to view" width="100%">
</div>

次の手順は、モデルからビューへのデータフローの概要です。

1. ユーザーは`favoriteColorControl.setValue()`メソッドを呼び出します。これは`FormControl`の値を更新します。
1. `FormControl`インスタンスは`valueChanges`オブジェクトを通して新しい値を発行します。
1. `valueChanges`Observableのサブスクライバーが新しい値を受け取ります。
1. フォームのinput要素のコントロール値アクセサは、要素を新しい値で更新します。

### テンプレート駆動フォームでのデータフロー

テンプレート駆動フォームでは、各form要素はフォームモデルを内部的に管理するディレクティブにリンクされています。次の図では、同じお気に入りの色の例を使用して、入力フィールドの値がビューから変更されたときと、モデルから変更されたときのデータフローを説明しています。

<div class="lightbox">
  <img src="generated/images/guide/forms-overview/dataflow-td-forms-vtm.png" alt="Template-driven forms data flow - view to model" width="100%">
</div>

次の手順は、入力値が*Red*から*Blue*に変化したときのビューからモデルへのデータフローの概要です。

1. ユーザーがinput要素に*Blue*と入力します。
1. input要素は値*Blue*をもつ"input"イベントを発行します。
1. input要素にアタッチされているコントロール値アクセサは`FormControl`インスタンスの`setValue()`メソッドをトリガーします。
1. `FormControl`インスタンスは`valueChanges`オブジェクトを通して新しい値を発行します。
1. `valueChanges`Observableのサブスクライバーが新しい値を受け取ります。
1. コントロール値アクセサは`ngModelChange`イベントを発行する`NgModel.viewToModelUpdate()`メソッドも呼び出します。
1. コンポーネントテンプレートは`favoriteColor`プロパティに双方向データバインディングを使っているので、コンポーネントの`favoriteColor`プロパティは
`ngModelChange`イベント(*Blue*)によって発行された値に更新されます。

<div class="lightbox">
  <img src="generated/images/guide/forms-overview/dataflow-td-forms-mtv.png" alt="Template-driven forms data flow - model to view" width="100%">
</div>

次の手順は、`favoriteColor`が*Blue*から*Red*に変わったときのモデルからビューへのデータフローの概要です。

1. コンポーネントの`favoriteColor`の値が更新されます。
1. 変更検知が開始します。
1. 変更検知中に、その入力の1つの値が変更されたので、`ngOnChanges`ライフサイクルフックが`NgModel`ディレクティブインスタンスで呼び出されます。
1. `ngOnChanges()`メソッドは、内部の`FormControl`インスタンスの値を設定するための非同期タスクをキューに入れます。
1. 変更検知が完了します。
1. 次のtickで、`FormControl`インスタンス値を設定するためのタスクが実行されます。
1. `FormControl`インスタンスは`valueChanges`オブジェクトを通して最新の値を発行します。
1. `valueChanges`Observableのサブスクライバーが新しい値を受け取ります。
1. コントロール値アクセサは、ビュー内のフォームのinput要素を最新の`favoriteColor`値で更新します。

## フォームバリデーション

バリデーションは、一連のフォームを管理するための不可欠な部分です。あなたが必須のフィールドをチェックしているか既存のユーザー名のために外部のAPIを問い合わせているかにかかわらず、Angularはカスタムバリデータを作成する機能と同様にビルトインバリデータのセットを提供します。

* **リアクティブフォーム** はバリデーションするためのコントロールを受け取る **関数** としてカスタムバリデータを定義します。
* **テンプレート駆動フォーム** はテンプレート **ディレクティブ** に関連付けられており、バリデート関数をラップするカスタムバリデータディレクティブを提供する必要があります。

詳細については[フォームバリデーション](guide/form-validation)を参照してください。

## テスト

テストは複雑なアプリケーションで大きな役割を果たし、フォームが正しく機能していることを検証するときには、シンプルなテスト戦略が役立ちます。リアクティブフォームとテンプレート駆動フォームは、フォームコントロールとフォームフィールドの変更に基づいてアサーションを実行するためのUIのレンダリングに異なるレベルの依存性を持っています。次の例では、リアクティブフォームとテンプレート駆動フォームを使用してフォームをテストするプロセスを説明します。

### リアクティブフォームをテストする

リアクティブフォームはフォームとデータモデルへの同期アクセスを提供するので比較的簡単なテスト戦略を提供し、UIをレンダリングせずにテストすることができます。これらのテストでは、状態とデータは、変更検知サイクルと相互作用することなく、コントロールを通して参照および操作されます。

次のテストでは、リアクティブフォームについて、ビューからモデル、およびモデルからビューへのデータフローを検証するために、さきほどのお気に入りの色コンポーネントを使用します。

ビューからモデルへのデータフローを検証するテストは次のようになります。

<code-example path="forms-overview/src/app/reactive/favorite-color/favorite-color.component.spec.ts" region="view-to-model" header="Favorite color test - view to model">
</code-example>

ビューからモデルでのテストで実行される手順は次のとおりです。

1. フォームのinput要素についてビューを参照し、テスト用のカスタムの"input"イベントを作成します。
1. input要素の新しい値を*Red*に設定して、フォームのinput要素に"input"イベントをディスパッチします。
1. コンポーネントの`favoriteColorControl`の値がinput要素からの値と一致することをアサートします。

モデルからビューへのデータフローを検証するテストは次のようになります。

<code-example path="forms-overview/src/app/reactive/favorite-color/favorite-color.component.spec.ts" region="model-to-view" header="Favorite color test - model to view">
</code-example>

モデルからビューでのテストで実行される手順は次のとおりです。

1. 新しい値を設定するために、`FormControl`インスタンスである`favoriteColorControl`を使います。
1. フォームのinput要素のビューを参照します。
1. コントロールに設定された新しい値がinput要素の値と一致することをアサートします。

### テンプレート駆動フォームをテストする

テンプレート駆動フォームでテストを書くには、変更検知プロセスに関する詳細な知識と、各サイクルでディレクティブがどのように実行されるかを理解して、要素が正しいタイミングで参照、テスト、または変更されるようにする必要があります。

次のテストでは、テンプレート駆動フォームのビューからモデルへ、およびモデルからビューへのデータフローを検証するために、さきほどのお気に入りの色コンポーネントを使用します。

ビューからモデルへのデータフローを検証するテストは次のようになります。

<code-example path="forms-overview/src/app/template/favorite-color/favorite-color.component.spec.ts" region="view-to-model" header="Favorite color test - view to model">
</code-example>

ビューからモデルのテストで実行される手順は次のとおりです。

1. フォームのinput要素についてビューを参照し、テスト用のカスタムの"input"イベントを作成します。
1. input要素の新しい値を*Red*に設定して、フォームのinput要素に"input"イベントをディスパッチします。
1. テストフィクスチャを通して変化検知を実行します。
1. コンポーネントの`favoriteColor`プロパティ値がinput要素の値と一致することをアサートします。

モデルからビューへのデータフローを検証するテストは次のようになります。

<code-example path="forms-overview/src/app/template/favorite-color/favorite-color.component.spec.ts" region="model-to-view" header="Favorite color test - model to view">
</code-example>

モデルからビューのテストで実行される手順は次のとおりです。

1. `favoriteColor`プロパティの値を設定するためにコンポーネントインスタンスを使います。
1. テストフィクスチャを通して変化検知を実行します。
1. `tick()`メソッドを使って`fakeAsync()`タスク内の時間の経過をシミュレートします。
1. フォームのinpuy要素のビューを参照します。
1. input要素の値がコンポーネントインスタンスの`favoriteColor`プロパティの値と一致することをアサートします。

## ミュータビリティ

変更を監視する方法は、アプリケーションの効率性に重要な役割を果たします。


* **リアクティブフォーム**はイミュータブルなデータ構造としてそれを提供することによってデータモデルを純粋に保ちます。データモデルに変更が引き起こされるたびに、`FormControl`インスタンスは既存のデータモデルを更新するのではなく新しいデータモデルを返します。これにより、コントロールのObservableを通じて、データモデルに対する一意の変更を監視することができます。一意の変更に対してのみ更新すればよいので、変更検知をより効率的にするために単方向のデータフローを提供します。また、データを変形するためにObservableオペレーターと統合するリアクティブパターンに従います。

* **テンプレート駆動フォーム**は、テンプレートに変更が加えられたときにコンポーネント内のデータモデルを更新するために双方向のデータバインディングによるミュータビリティに依存しています。双方向データバインディングを使用する場合、データモデルを監視するための一意の変更はないため、変更検知は、更新が必要になるタイミングを判断するには効率的ではありません。

上記の例では、**お気に入りの色** input要素を使用して違いを説明しています。


* リアクティブフォームでは、コントロールの値が更新されたときに **`FormControl` インスタンス** は常に新しい値を返します。

* テンプレート駆動フォームでは、**お気に入りのカラープロパティ** は常に新しい値に変更されます。

## スケーラビリティ

フォームがアプリケーションの中心的な部分である場合、スケーラビリティは非常に重要です。コンポーネント間でフォームモデルを再利用できることが重要です。


* **リアクティブフォーム**は、低レベルAPIへのアクセスとフォームモデルへの同期アクセスを提供し、大規模フォームの作成を容易にします。

* **テンプレート駆動型**フォームはシンプルなシナリオに焦点を当て、再利用可能ではなく、低レベルAPIを抽象化し、フォームモデルへの非同期アクセスを提供します。テンプレート駆動フォームによる抽象化はテストにおいてもフォームが表面化します。リアクティブフォームのテストの場合、必要なセットアップが少なく、テスト中にフォームとデータモデルを更新して検証するときに変更検知サイクルに依存しません。

## まとめ

戦略を選択することは提示された選択肢の長所と短所を理解することから始まります。Angularでフォームを構築するために使用するインフラストラクチャを選択する際には、低レベルのAPIとフォームモデルへのアクセス、プレディクタビリティ、ミュータビリティ、直接的なバリデーションとテスト戦略、およびスケーラビリティがすべて重要な考慮事項です。テンプレート駆動型フォームはAngularJSのパターンに似ていますが、現代の大規模なAngularアプリケーションの多くの基準を考慮すると、制限があります。リアクティブフォームはこれらの制限を最小限に抑えます。リアクティブフォームは、Angularアーキテクチャの他の分野にすでに存在するリアクティブパターンと統合され、それらの要件を十分に補完します。

## 次のステップ



リアクティブフォームの詳細については次のガイドを参照してください:

* [リアクティブフォーム](guide/reactive-forms)
* [フォームバリデーション](guide/form-validation#reactive-form-validation)
* [ダイナミックフォーム](guide/dynamic-form)

テンプレート駆動フォームの詳細については次のガイドを参照してください:

* [テンプレート駆動フォーム](guide/forms#template-driven-forms)
* [フォームバリデーション](guide/form-validation#template-driven-validation)
