---
aliases:
- /ja/real_user_monitoring/guide/getting-started-feature-flags/
beta: true
description: RUM をセットアップして機能フラグデータをキャプチャし、Datadog でパフォーマンスを分析する方法をご紹介します。
further_reading:
- link: /real_user_monitoring/feature_flag_tracking
  tag: ドキュメント
  text: 機能フラグ追跡で機能フラグデータを分析する
- link: /real_user_monitoring/explorer
  tag: Documentation
  text: RUM エクスプローラーで RUM データを視覚化する
kind: ガイド
title: RUM の機能フラグデータの概要
---

<div class="alert alert-warning">
    機能フラグ追跡はベータ版です。
</div>


## 概要
機能フラグデータにより、どのユーザーに特定の機能が表示されているか、導入した変更がユーザー体験に影響を与えているか、パフォーマンスに悪影響を与えているかを判断できるため、ユーザー体験やパフォーマンス監視の可視性が高まります。

RUM データを機能フラグデータでリッチ化することにより、意図せずにバグやパフォーマンスの低下を引き起こすことなく、その機能が正常に起動することを確信することができます。この追加的な洞察により、機能のリリースとパフォーマンスを関連付け、問題を特定のリリースにピンポイントで特定し、より迅速にトラブルシューティングを行うことができます。

## セットアップ

{{< tabs >}}
{{% tab "ブラウザ" %}}

機能フラグの追跡は、RUM ブラウザ SDK で利用可能です。開始するには、[RUM ブラウザモニタリング][1]をセットアップします。ブラウザ RUM SDK バージョン >= 4.25.0 が必要です。

機能フラグデータの収集を開始するには、RUM SDK を初期化し、` ["feature_flags"]` で `enableExperimentalFeatures` 初期化パラメーターを構成します。

<details open>
  <summary>npm</summary>

```javascript
  import { datadogRum } from '@datadog/browser-rum';

  // Datadog ブラウザ SDK を初期化します
  datadogRum.init({
    ...
    enableExperimentalFeatures: ["feature_flags"],
    ...
});
```

</details>

<details>
  <summary>CDN async</summary>

```javascript
window.DD_RUM.onReady(function() {
    window.DD_RUM.init({
      ...
      enableExperimentalFeatures: ["feature_flags"],
      ...
    })
})
```
</details>

<details>
  <summary>CDN sync</summary>

```javascript
window.DD_RUM &&
    window.DD_RUM.init({
      ...
      enableExperimentalFeatures: ["feature_flags"],
      ...
    })
```
</details>
<br/>

[1]: /ja/real_user_monitoring/browser#setup
{{% /tab %}}
{{% tab "iOS" %}}

機能フラグの追跡は、RUM iOS SDK で利用可能です。開始するには、[RUM iOS モニタリング][1]をセットアップします。iOS RUM SDK バージョン 1.16.0 以上が必要です。

[1]: https://docs.datadoghq.com/ja/real_user_monitoring/ios/?tab=swift
{{% /tab %}}
{{% tab "Android" %}}

機能フラグの追跡は、RUM Android SDK で利用可能です。開始するには、[RUM Android モニタリング][1]をセットアップします。Android RUM SDK バージョン 1.18.0 以上が必要です。

[1]: https://docs.datadoghq.com/ja/real_user_monitoring/android/?tab=kotlin
{{% /tab %}}
{{% tab "Flutter" %}}

機能フラグの追跡は、Flutter アプリケーションで利用可能です。開始するには、[RUM Flutter モニタリング][1]をセットアップします。Flutter プラグインバージョン 1.3.2 以上が必要です。

[1]: https://docs.datadoghq.com/ja/real_user_monitoring/flutter/
{{% /tab %}}
{{< /tabs >}}

## インテグレーション

機能フラグデータの収集は、[カスタム機能フラグ管理ソリューション](#custom-feature-flag-management)、または Datadog のインテグレーションパートナーのいずれかを使用して開始することができます。

Datadog は、以下とのインテグレーションをサポートしています。
{{< partial name="rum/rum-feature-flag-tracking.html" >}}


</br>

### カスタム機能フラグ管理

{{< tabs >}}
{{% tab "ブラウザ" %}}

機能フラグが評価されるたびに、以下の関数を追加して、機能フラグの情報を RUM に送信します。

```javascript
datadogRum.addFeatureFlagEvaluation(key, value);
```

{{% /tab %}}
{{% tab "iOS" %}}

機能フラグが評価されるたびに、以下の関数を追加して、機能フラグの情報を RUM に送信します。

   ```javascript
   Global.rum.addFeatureFlagEvaluation(key, value);
   ```

{{% /tab %}}
{{% tab "Android" %}}

機能フラグが評価されるたびに、以下の関数を追加して、機能フラグの情報を RUM に送信します。

   ```javascript
   GlobalRum.get().addFeatureFlagEvaluation(key, value);
   ```

{{% /tab %}}
{{< /tabs >}}

### LaunchDarkly インテグレーション

{{< tabs >}}
{{% tab "ブラウザ" %}}

LaunchDarkly の SDK を初期化し、以下に示すコードスニペットを使用して、Datadog に機能フラグの評価を報告するインスペクターを作成します。

LaunchDarkly の SDK の初期化については、[LaunchDarkly の JavaScript SDK ドキュメント][1]を参照してください。

```javascript
const client = LDClient.initialize("<APP_KEY>", "<USER_ID>", {
  inspectors: [
    {
      type: "flag-used",
      name: "dd-inspector",
      method: (key: string, detail: LDClient.LDEvaluationDetail) => {
        datadogRum.addFeatureFlagEvaluation(key, detail.value);
      },
    },
  ],
});
```


[1]: https://docs.launchdarkly.com/sdk/client-side/javascript#initializing-the-client
{{% /tab %}}
{{% tab "iOS" %}}

LaunchDarkly は現在、このインテグレーションをサポートしていません。この機能をリクエストするには、LaunchDarkly でチケットを作成してください。


{{% /tab %}}
{{% tab "Android" %}}

LaunchDarkly は現在、このインテグレーションをサポートしていません。この機能をリクエストするには、LaunchDarkly でチケットを作成してください。


{{% /tab %}}
{{< /tabs >}}


### Split インテグレーション

{{< tabs >}}
{{% tab "ブラウザ" %}}

Split の SDK を初期化し、以下に示すコードスニペットを使用して Datadog に機能フラグの評価を報告するインプレッションリスナーを作成します

Split の SDK の初期化については、[Split の JavaScript SDK ドキュメント][1]を参照してください。

```javascript
const factory = SplitFactory({
    core: {
      authorizationKey: "<APP_KEY>",
      key: "<USER_ID>",
    },
    impressionListener: {
      logImpression(impressionData) {
          datadogRum
              .addFeatureFlagEvaluation(
                  impressionData.impression.feature,
                  impressionData.impression.treatment
              );
    },
  },
});

const client = factory.client();
```


[1]: https://help.split.io/hc/en-us/articles/360020448791-JavaScript-SDK#2-instantiate-the-sdk-and-create-a-new-split-client
{{% /tab %}}
{{% tab "iOS" %}}

Split の SDK を初期化し、以下に示すコードスニペットを使用して、Datadog に機能フラグの評価を報告するインスペクターを作成します。

Split の SDK の初期化については、[Split の iOS SDK ドキュメント][1]を参照してください。

```javascript
  let config = SplitClientConfig()
// Split がインプレッションを報告する際に機能フラグを送信します
  config.impressionListener = { impression in
      if let feature = impression.feature,
          let treatment = impression.treatment {
          Global.rum.addFeatureFlagEvaluation(name: feature, value: treatment)
      }
  }
```


[1]: https://help.split.io/hc/en-us/articles/360020401491-iOS-SDK
{{% /tab %}}
{{% tab "Android" %}}

Split の SDK を初期化し、以下に示すコードスニペットを使用して、Datadog に機能フラグの評価を報告するインスペクターを作成します。

Split の SDK の初期化については、[Split の Android SDK ドキュメント][1]を参照してください。

```javascript
  internal class DatadogSplitImpressionListener : ImpressionListener {
    override fun log(impression: Impression) {
        // Split がインプレッションを報告する際に機能フラグを送信します
        GlobalRum.get().addFeatureFlagEvaluation(
            impression.split(),
            impression.treatment()
        )
    }
    override fun close() {
    }
  }

  // 初期化時:
  val apikey = BuildConfig.SPLIT_API_KEY
  val config = SplitClientConfig.builder()
      .impressionListener(DatadogSplitImpressionListener())
      .build()
```


[1]: https://help.split.io/hc/en-us/articles/360020343291-Android-SDK
{{% /tab %}}
{{< /tabs >}}


### Flagsmith インテグレーション

{{< tabs >}}
{{% tab "ブラウザ" %}}

Flagsmith の SDK に `datadogRum` オプションを付けて初期化すると、以下に示すコードのスニペットを使用して Datadog に機能フラグの評価を報告することができるようになります。

 オプションとして、`datadogRum.setUser()` を介して Flagsmith の Trait が Datadog に送信されるようにクライアントを構成することができます。Flagsmith の SDK の初期化についての詳細は、[Flagsmith の JavaScript SDK ドキュメント][1]を参照してください。

   ```javascript
    // Flagsmith SDK を初期化します
    flagsmith.init({
        datadogRum: {
            client: datadogRum,
            trackTraits: true,
        },
        ...
    })
   ```


[1]: https://docs.flagsmith.com/clients/javascript
{{% /tab %}}
{{% tab "iOS" %}}

Flagsmith は現在、このインテグレーションをサポートしていません。この機能をリクエストするには、Flagsmith でチケットを作成してください。


{{% /tab %}}
{{% tab "Android" %}}

Flagsmith は現在、このインテグレーションをサポートしていません。この機能をリクエストするには、Flagsmith でチケットを作成してください。

{{% /tab %}}
{{< /tabs >}}

## RUM で機能フラグのパフォーマンスを分析する

機能フラグは、RUM のセッション、ビュー、およびエラーのコンテキストにリストとして表示されます。

{{< img src="real_user_monitoring/guide/setup-feature-flag-data-collection/feature-flag-list-rum-event.png" alt="RUM エクスプローラーの属性の機能フラグリスト" style="width:75%;">}}

### RUM エクスプローラーを使用した機能フラグの検索
[RUM エクスプローラー][2]で RUM が収集したすべてのデータを検索し、機能フラグの傾向を把握したり、より大きな文脈でパターンを分析したり、[ダッシュボード][3]や[モニター][4]にエクスポートしたりすることが可能です。RUM エクスプローラーでは、`@feature_flags.{flag_name}` 属性でセッション、ビュー、またはエラーを検索することができます。

#### セッション
**Sessions** を `@feature_flags.{flag_name}` 属性でフィルタリングすると、指定した時間枠で機能フラグが評価されたすべてのセッションを見つけることができます。

{{< img src="real_user_monitoring/guide/setup-feature-flag-data-collection/rum-explorer-session-feature-flag-search.png" alt="RUM エクスプローラーでの機能フラグのセッション検索" style="width:75%;">}}

#### ビュー
**Views** を `@feature_flags.{flag_name}` 属性でフィルタリングすると、指定した時間枠で機能フラグが評価された特定のビューを見つけることができます。

{{< img src="real_user_monitoring/guide/setup-feature-flag-data-collection/rum-explorer-view-feature-flag-search.png" alt="RUM エクスプローラーでの機能フラグのビュー検索" style="width:75%;">}}

#### エラー
**Errors** を `@feature_flags.{flag_name}` 属性でフィルタリングすると、指定した時間枠で機能フラグが評価されたビューで発生したすべてのエラーを見つけることができます。

{{< img src="real_user_monitoring/guide/setup-feature-flag-data-collection/rum-explorer-error-feature-flag-search.png" alt="RUM エクスプローラーでの機能フラグのエラー検索" style="width:75%;">}}

### 機能フラグのデータが期待通りに反映されないのはなぜですか？
機能フラグは、それが評価されるイベントのコンテキストに表示されます。つまり、機能フラグのコードロジックが実行されるビューに表示されるはずです。

コードの構成や機能フラグの設定によっては、予期せぬ機能フラグがイベントのコンテキストに表示されることがあります。

例えば、機能フラグがどの**ビュー**で評価されているかを確認するには、RUM エクスプローラーを使用して同様のクエリを行うことができます。


{{< img src="real_user_monitoring/guide/setup-feature-flag-data-collection/feature_flag_view_query.png" alt="RUM エクスプローラーでの機能フラグのビュー検索" style="width:75%;">}}


機能フラグが無関係なビューで評価されている理由の例をいくつか紹介しますので、調査の参考にしてください。

- 実行されるたびに機能フラグを評価する、複数のページに表示される共通のリアクトコンポーネント。
- 機能フラグを評価したコンポーネントが、URL の変更前/変更後にレンダリングされるルーティングの問題。

調査を行う際、機能フラグに関連する `View Name` のデータをスコープすることも可能です。

## その他の参考資料
{{< partial name="whats-next/whats-next.html" >}}

[2]: https://app.datadoghq.com/rum/explorer
[3]: /ja/dashboards/
[4]: /ja/monitors/#create-monitors