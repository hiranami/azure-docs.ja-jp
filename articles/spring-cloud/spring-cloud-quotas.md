---
title: Azure Spring Cloud のサービス プランとクォータ
description: Azure Spring Cloud のサービス クォータとサービス プランについて説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278888"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure Spring Cloud のクォータとサービス プラン

すべての Azure サービスには、リソースと機能に対する既定の制限とクォータが設定されています。  プレビュー期間中は、Azure Spring Cloud で提供されるサービス プランは 1 つだけです。

この記事では、現在のプレビュー期間中に提供されるサービス クォータについて詳しく説明します。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure Spring Cloud のサービス レベルとクォータ

プレビュー期間中は、Azure Spring Cloud で提供されるサービス レベルは 1 つだけです。

リソース | 金額
------- | -------
vCPU | サービス インスタンスごとに 4 つ
メモリ | サービス インスタンスごとに 8 GB
サブスクリプション 1 件、1 リージョンあたりの Azure Spring Cloud サービス インスタンスの数 | 10
Azure Spring Cloud サービス インスタンスあたりのアプリ インスタンスの合計数 | 500
Spring アプリケーションあたりのアプリ インスタンスの合計数 | 20
永続ボリューム | 10 x 50 GB

クォータに達すると、次のような 400 エラーを受け取ります: "Quota exceeds limit for subscription *your subscription* in region *region where your Azure Spring Cloud service is created* (クォータがサブスクリプション <サブスクリプション> のリージョン <Azure Spring Cloud サービスが作成されたリージョン> での制限を超えています)。

## <a name="next-steps"></a>次のステップ

特定の既定の制限とクォータを増やすことができます。 リソースを増やす必要がある場合は、[サポート リクエストを作成してください](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。
