---
title: 適切な価格レベルの選択 | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps が提供する価格レベルについて説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8764e9161f952118ca7ae28343dcd16477cf1eee
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155759"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps での適切な価格レベルの選択

Azure Maps には、S0 と S1 という 2 つの価格レベルが用意されています。 この記事は、ニーズにあった適切な価格レベルを選択するのに役立ちます。 適切な価格レベルを選択するには、次の 2 つの質問を自分に尋ねてください。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>どのような地理空間機能を使用する予定ですか
自分のサービス要件がコア地理空間 API によって満たされる場合は、S0 価格レベルが適しています。 アプリケーションでより高度な機能が必要な場合は、S1 価格レベルを選択することを検討してください。 高度な機能の例:航空写真とハイブリッド画像、ルート範囲の取得、バッチ ジオコーディング。 「**価格レベルの機能**」の表は、アプリケーションに最適な価格レベルを選択するために役立ちます。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>サポートする同時実行ユーザーの数はどれくらいですか 
S0 価格レベルと S1 価格レベルでは、処理されるデータ スループットの量が異なります。 S0 価格レベルでは、**1 秒あたり最大 50 個のクエリ**が処理されます。また、S1 レベルでは、**1 秒あたり 50 個を超えるクエリ**が処理されます。

### <a name="pricing-tier-capabilities"></a>価格レベルの機能

| 機能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| マップの表示                              | ✓                   | ✓       |
| 衛星映像                       |                     | ✓        |
| 検索                                  | ✓                    | ✓        |
| バッチ検索                            |                     | ✓        |
| ルート                                   | ✓                    |✓        |
| バッチ ルーティング                            |                    | ✓        |
| マトリックス ルーティング                          |                     | ✓        |
| ルート範囲 (等時線)                |                     | ✓        |
| トラフィック                                |✓                    |✓        |
| タイム ゾーン                               |✓                    |✓        |
| 位置情報 (プレビュー)                    |✓                   |✓        |
| Spatial Operations                        |                    |✓        |
| ジオフェンシング                                |                    |✓        |
| Azure Maps Data (Preview)                |                     | ✓        |
| Mobility (プレビュー)                       |                     | ✓        |
| Weather (プレビュー)                        |✓                    |✓        |

次のデータ ポイントも検討に値します。
* どのような種類の企業か。
* アプリケーションはどのくらい重要か。

### <a name="pricing-tier-targeted-customers"></a>価格レベルの対象のお客様

S0 および S1 の価格レベルをさらによく理解するには、「**価格レベルの対象のお客様**」の表をご覧ください。 詳細については、「[Azure Maps の価格](https://azure.microsoft.com/pricing/details/azure-maps/)」をご覧ください。 

| Pricing tier  |     対象となるお客様                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 価格レベルは、概念実証開発や早い段階のテストからアプリケーションの運用やデプロイまで、すべての運用ステージのアプリケーションで利用できます。 ただし、このレベルは、小規模開発、同時ユーザー数の少ないお客様、またはその両方向けに設計されています。 <p>|
| S1            |    <p>S1 価格レベルは、大企業や、ミッションクリティカルなアプリケーション、多数の同時実行ユーザーのサポートを必要とするお客様に適しています。 また、高度な地理空間サービスを必要とするお客様にも適しています。</p>|

## <a name="next-steps"></a>次のステップ

価格レベルを表示および変更する方法について詳しく学びます。

> [!div class="nextstepaction"] 
> [価格レベルを管理する](how-to-manage-pricing-tier.md)
