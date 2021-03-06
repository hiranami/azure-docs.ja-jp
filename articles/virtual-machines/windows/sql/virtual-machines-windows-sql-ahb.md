---
title: Azure での SQL Server VM のライセンス モデルを変更する
description: Azure ハイブリッド特典を使用して、Azure の SQL Server 仮想マシンのライセンスを従量課金制からライセンス持ち込みに切り替える方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 00262b48b8fa2fd1292554155e8ec8e933d886e6
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690906"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure での SQL Server 仮想マシンのライセンス モデルを変更する
この記事では、新しい SQL VM リソース プロバイダーである **Microsoft.SqlVirtualMachine** を使用して Azure 内の SQL Server 仮想マシン (VM) のライセンス モデルを変更する方法について説明します。

SQL Server をホストする VM には、従量課金制と Azure ハイブリッド特典の 2 種類のライセンス モデルがあります。 ご利用の SQL Server VM のライセンス モデルは、Azure portal、Azure CLI、または PowerShell を使用して変更することができます。 

従量課金制モデルでは、Azure VM を実行する秒単位のコストに SQL Server ライセンスのコストが含まれます。
[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)では、SQL Server を実行する VM に対して独自の SQL Server ライセンスを使用することができます。 

Azure ハイブリッド特典では、Azure 仮想マシン上で SQL Server ライセンスをソフトウェア アシュアランス ("条件を満たしたライセンス") 付きで使用できます。 Azure ハイブリッド特典の場合、VM 上での SQL Server ライセンスの使用に対してお客様は課金されません。 ただし、基になるクラウド コンピューティング (基本料金)、ストレージ、およびバックアップのコストについては、引き続き料金を支払う必要があります。 また、サービスの使用に関連付けられている I/O についても支払う必要があります (該当する場合)。

Microsoft 製品の利用規約に従って、"お客様は、Azure 上でワークロードを構成時に、SQL Server 向け Azure ハイブリッド特典に基づいて Azure SQL Database (Managed Instance、Elastic Pool、Single Database)、Azure Data Factory、SQL Server Integration Services、または SQL Server Virtual Machines を使用していることを示す必要があります。"

Azure VM 上の SQL Server 向け Azure ハイブリッド特典を使用していることと、準拠していることを示すには、次の 3 つのオプションがあります。

- Azure Marketplace からのライセンス持ち込み SQL Server イメージを使用して、仮想マシンをプロビジョニングします。 このオプションは、マイクロソフト エンタープライズ契約を結んでいるお客様のみが利用できます。
- Azure Marketplace からの従量課金制の SQL Server イメージを使用して、仮想マシンをプロビジョニングし、Azure ハイブリッド特典をアクティブにします。
- Azure VM に SQL Server をセルフインストールし、手動で [SQL Server VM を登録](virtual-machines-windows-sql-register-with-resource-provider.md) をして、Azure ハイブリッド特典をアクティブにします。

SQL Server のライセンスの種類は、VM がプロビジョニングされるときに設定されます。 これは後でいつでも変更できます。 ライセンス モデル間の切り替えを行っても、ダウンタイムは発生せず、VM や SQL Serverサービスが再起動されたり、追加コストが追加されることもなく、すぐに有効になります。 実際には、Azure ハイブリッド特典をアクティブにするとコストが*削減*されます。

## <a name="prerequisites"></a>前提条件

SQL Server VM のライセンスモデルを変更するには、次の要件があります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL VM リソース プロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md)に登録された [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。
- [ソフトウェアアシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) は、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用するための要件です。 


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>リソース プロバイダーに既に登録されている VM のライセンスを変更する 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

ライセンス モデルは、ポータルから直接変更できます。 

1. [Azure portal](https://portal.azure.com) を開き、ご利用の SQL Server VM 用の [SQL 仮想マシン リソース](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)を開きます。 
1. **[設定]** の **[構成]** を選択します。 
1. **[Azure ハイブリッド特典]** オプションを選択し、ソフトウェア アシュアランス付きの SQL Server ライセンスがあることを確認するチェックボックスをオンにします。 
1. **[構成]** ページの下部にある **[適用]** を選択します。 

![ポータル内の Azure ハイブリッド特典](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、ご利用のライセンス モデルを変更することができます。  

次のコード スニペットでは、従量課金制ライセンス モデルをライセンス持ち込み (または Azure ハイブリッド特典の使用) に切り替えます。

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

次のコード スニペットは、BYOL モデルを従量課金制に切り替えます。 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell を使用して、ご利用のライセンス モデルを変更できます。

次のコード スニペットでは、従量課金制ライセンス モデルをライセンス持ち込み (または Azure ハイブリッド特典の使用) に切り替えます。

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

次のコード スニペットは、BYOL モデルを従量課金制に切り替えます。

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>リソース プロバイダーに登録されていない VM 用のライセンスを変更する

従量課金制 Azure Marketplace イメージから SQL Server VM をプロビジョニングした場合、SQL Server ライセンスの種類は従量課金制になります。 Azure Marketplace からのライセンス持ち込みイメージを使用して SQL Server VM をプロビジョニングした場合、ライセンスの種類は AHUB になります。 既定 (従量課金制) またはライセンス持ち込み Azure Marketplace イメージからプロビジョニングされた SQL Server VM はすべて、SQL VM リソース プロバイダーに自動的に登録されます。その結果、[ライセンスの種類](#change-the-license-for-vms-already-registered-with-the-resource-provider)の変更が可能になります。

SQL Server については、Azure ハイブリッド特典を介して Azure VM にセルフインストールすることのみが可能です。 Microsoft 製品の利用規約に従って Azure ハイブリッド特典の使用状況を示すには、SQL Server ライセンスを Azure ハイブリッド特典に設定して、[これらの VM を SQL VM リソース プロバイダーに登録](virtual-machines-windows-sql-register-with-resource-provider.md)する必要があります。

SQL Server VM が SQL VM リソースプロバイダーに登録されている場合にのみ、SQL Server VM のライセンスの種類を従量課金制または Azure ハイブリッド特典に変更できます。

## <a name="remarks"></a>解説

- Azure Cloud Solution Provider (CSP) のお客様は、アクティブなソフトウェア アシュアランスを所有している場合、従量課金制の VM をデプロイしてからそれをライセンス持ち込みに変換することで、Azure ハイブリッド特典を利用できるようになります。
- SQL Server VM リソースを削除する場合は、イメージのハード コーディングされたライセンス設定に戻ります。 
- ライセンス モデルを変更する機能は、SQL VM リソース プロバイダーの機能です。 Azure portal を介して Azure Marketplace イメージをデプロイすると、SQL Server VM がリソース プロバイダーに自動的に登録されます。 ただし、SQL Server をセルフインストールするお客様は、手動で [SQL Server VM を登録](virtual-machines-windows-sql-register-with-resource-provider.md)する必要があります。 
- SQL Server VM を可用性セットに追加するには、VM を再作成する必要があります。 そのため、可用性セットに追加された VM はいずれも、既定のライセンスの種類である従量課金制に戻ることになります。 Azure ハイブリッド特典を再び有効にする必要があります。 


## <a name="limitations"></a>制限事項

ライセンスモデルの変更は次のとおりです。
   - [ソフトウェアアシュアランス](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview) をお持ちのお客様のみご利用いただけます。
   - SQL Server の Standard および Enterprise エディションでのみサポートされています。 Express、Web、Developer のライセンス変更はサポートされていません。 
   - Azure Resource Manager モデルを介してデプロイされた仮想マシンでのみサポートされます。 クラシック モデルを介してデプロイされた仮想マシンはサポートされていません。 
   - パブリック クラウドまたは Azure Government クラウドでのみ使用できます。 
   - 1 つのネットワークインターフェイス (NIC) を持つ仮想マシンでのみサポートされます。 


## <a name="known-errors"></a>既知のエラー

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>リソース 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' が見つかりませんでした。

このエラーは、SQL Server VM 上で SQL VM リソース プロバイダーに登録されていないライセンス モデルを変更しようとしたときに発生します。

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

リソース プロバイダーをサブスクリプションに登録してから、[そのリソース プロバイダーにご利用の SQL Server VM を登録する](virtual-machines-windows-sql-register-with-resource-provider.md)必要があります。 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>仮想マシン「\<VMName\>」に複数の NIC が関連付けられています

このエラーは、複数の NIC を持つ仮想マシンで発生します。 ライセンスモデルを変更する前に、いずれかの NIC を削除します。 ライセンスモデルを変更した後に NIC を VM に再び追加することはできますが、自動バックアップや修正プログラム適用などの Azure portal の操作はサポートされなくなります。 


## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


