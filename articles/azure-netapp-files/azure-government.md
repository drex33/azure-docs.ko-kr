---
title: Azure Government | 대한 Azure NetApp Files Microsoft Docs
description: Azure Government 연결하여 Azure NetApp Files 사용하고 Azure Government Azure NetApp Files 기능 가용성을 사용하는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: b-juche
ms.openlocfilehash: 3e820abf824a9593c265fd7d9d4c6ccd9be5cb38
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047592"
---
# <a name="azure-netapp-files-for-azure-government"></a>Azure Government용 Azure NetApp Files 

[Microsoft Azure Government는](../azure-government/documentation-government-welcome.md) 정부 기관 및 해당 파트너가 중요 업무용 워크로드를 클라우드로 변환할 수 있는 전용 클라우드를 제공합니다.  

이 문서에서는 Azure Government Azure NetApp Files 기능 가용성에 대해 설명합니다. 또한 Azure Government 내에서 Azure NetApp Files 서비스에 액세스하는 방법을 보여줍니다.

## <a name="feature-availability"></a>기능 가용성

Azure NetApp Files 지원하는 Azure Government 지역은 *[지역별 사용 가능한 제품 페이지를 참조하세요.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)*  

Azure 퍼블릭 클라우드에서 사용할 수 있는 모든 [Azure NetApp Files 기능은](whats-new.md) ***다음 표에 나열된 기능을 제외하고*** 지원되는 Azure Government 지역에서도 사용할 수 있습니다. 

| Azure NetApp Files 기능 | Azure 퍼블릭 클라우드 가용성 |  Azure Government 가용성 |
|:--- |:--- |:--- |
| 지역 간 복제 Azure NetApp Files | GA(일반 공급) | [제한적](cross-region-replication-introduction.md#supported-region-pairs) |
| Azure NetApp Files 백업 | 퍼블릭 미리 보기 | 아니요 |

## <a name="portal-access"></a>포털 액세스

Azure Government 사용자는 브라우저에서 portal.azure.us 가리키면 **Azure NetApp Files** 액세스할 수 있습니다.포털 사이트 이름은 **정부 Microsoft Azure.**자세한 내용은 [포털을 사용하여 Azure Government 커넥트](../azure-government/documentation-government-get-started-connect-with-portal.md) 참조하세요.   

![URL로 portal.azure.us 강조 표시된 Azure Government Portal의 스크린샷](../media/azure-netapp-files/azure-government.jpg)

Microsoft Azure Government 포털에서 Azure Portal 것과 동일한 방식으로 Azure NetApp Files 액세스할 수 있습니다.예를 들어 포털의 리소스 검색 상자에 **Azure NetApp Files** 입력한 다음, 표시되는 목록에서 **Azure NetApp Files** 선택할 수 있습니다.  

서비스 사용에 대한 자세한 내용은 [Azure NetApp Files](/azure/azure-netapp-files/) 설명서를 참조하세요.

## <a name="azure-cli-access"></a>액세스 Azure CLI

클라우드 이름을 로 설정하여 Azure Government `AzureUSGovernment` 연결한 다음, 명령과 마찬가지로 로그인을 계속 진행할 수 `az login` 있습니다. 로그인 명령을 실행하면 적절한 Azure Government 자격 증명을 입력하는 브라우저가 시작됩니다.  

```azurecli 

az cloud set --name AzureUSGovernment 

``` 

클라우드가 로 설정되었는지 확인하려면 `AzureUSGovernment` 다음을 실행합니다. 

```azurecli 

az cloud list --output table 

``` 

이 명령은 Azure 클라우드 위치가 있는 테이블을 생성합니다. `isActive`에 대한 열 항목은 `AzureUSGovernment` 을 읽어야 `true` 합니다.  

자세한 내용은 [Azure CLI Azure Government 커넥트](../azure-government/documentation-government-get-started-connect-with-cli.md) 참조하세요.

## <a name="rest-api-access"></a>액세스 REST API

Azure Government 엔드포인트는 상용 Azure 엔드포인트와 다릅니다. 다른 엔드포인트 목록은 개발자를 위한 Azure Government 지침을 [참조하세요.](../azure-government/compare-azure-government-global-azure.md#guidance-for-developers)

## <a name="powershell-access"></a>PowerShell 액세스

PowerShell을 통해 Azure Government 연결할 때 올바른 엔드포인트에 연결할 수 있도록 환경 매개 변수를 지정해야 합니다. 이 위치에서 PowerShell과 마찬가지로 Azure NetApp Files 계속 사용할 수 있습니다. 

| 연결 형식 | 명령 | 
| --- | --- | 
| [Azure](/powershell/module/az.accounts/Connect-AzAccount) 명령 |`Connect-AzAccount -EnvironmentName AzureUSGovernment` | 
| [Azure Active Directory](/powershell/module/azuread/connect-azuread) 명령 |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` | 
| [Azure(클래식 배포 모델)](/powershell/module/servicemanagement/azure.service/add-azureaccount) 명령 |`Add-AzureAccount -Environment AzureUSGovernment` | 
| [Azure Active Directory(클래식 배포 모델)](/previous-versions/azure/jj151815(v=azure.100)) 명령 |`Connect-MsolService -AzureEnvironment UsGovernment` | 

자세한 내용은 [PowerShell을 사용하여 Azure Government 커넥트](../azure-government/documentation-government-get-started-connect-with-ps.md) 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Government란?](../azure-government/documentation-government-welcome.md)
* [Azure NetApp Files의 새로운 기능](whats-new.md)
* [Azure Government 및 글로벌 Azure 비교](../azure-government/compare-azure-government-global-azure.md)
* [Azure NetApp Files REST API](azure-netapp-files-develop-with-rest-api.md)
* [PowerShell을 사용하여 Azure NetApp Files REST API](develop-rest-api-powershell.md)
