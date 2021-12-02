---
title: Azure NetApp Files SDK 및 CLI 도구 | Microsoft Docs
description: Azure NetApp Files에서 지원되는 SDK, GitHub에서 게시된 위치, 지원되는 명령줄 도구인 Azure CLI와 PowerShell에 대해 알아보세요.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: b-hchen
ms.openlocfilehash: 87f04d697aacceee6302d2be913522e4c4449f18
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487476"
---
# <a name="azure-netapp-files-sdks-cli-tools-and-arm-templates"></a>Azure NetApp Files SDK, CLI 도구 및 ARM 템플릿

이 문서에는 Azure NetApp Files에서 지원하는 SDK, CLI(명령줄) 도구, ARM(Azure Resource Manager) 템플릿이 나열되어 있습니다.

## <a name="supported-sdks"></a>지원되는 SDK 

아래 표에는 지원되는 SDK가 나와 있습니다.  지원되는 SDK에 대한 자세한 내용은 GitHub의 게시된 위치에서 찾을 수 있습니다.  

|    언어    |    GitHub의 SDK 게시 위치    |
|------------------|--------------------------------------------------------------|
|    .NET  |    [Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/netapp)    |
|    Python  |  [Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/netapp)    |
|    Go    |    [Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/netapp)       |
|    Java |     [Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/netapp) |
|    JavaScript    |    [Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/netapp/arm-netapp)    |
|    Ruby   |    [Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_netapp)    |

## <a name="cli-tools"></a>CLI 도구

다음 표에는 지원되는 CLI 도구와 해당 명령 참조가 나열되어 있습니다.   

|    도구    |    명령 참조    |
|------------------|--------------------------------------------|
|    Azure CLI  |    [az netappfiles](/cli/azure/netappfiles)    |
|    PowerShell   |    [Azure NetApp Files에 대한 Azure PowerShell](/powershell/module/az.netappfiles/#netapp_files&preserve-view=true)    |

## <a name="code-samples"></a>코드 샘플

[Azure NetApp Files에 대한 코드 샘플](/samples/browse/?filter-products=netapp&products=azure-netapp-files)을 참조하세요.

## <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿  

ARM(Azure Resource Manager)을 사용하면 선언적 템플릿을 통해 애플리케이션을 프로비저닝할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 애플리케이션 수명 주기의 각 단계 중에 애플리케이션을 반복해서 배포합니다.   

[Azure NetApp Files에서 사용 가능한 ARM 템플릿](https://azure.microsoft.com/resources/templates/?term=anf)을 참조하세요.

## <a name="next-steps"></a>다음 단계   
 [Azure SDK 다운로드](https://azure.microsoft.com/downloads/)
