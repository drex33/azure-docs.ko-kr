---
title: Azure Government에 대 한 Azure NetApp Files | Microsoft Docs
description: Azure Government에서 Azure NetApp Files 및 Azure NetApp Files 기능 가용성을 사용 하기 위해 Azure Government에 연결 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 9e7986a0e040d0d8241fd33a2990d8bd82e51a4e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130230238"
---
# <a name="azure-netapp-files-for-azure-government"></a>Azure Government용 Azure NetApp Files 

[Microsoft Azure 정부](../azure-government/documentation-government-welcome.md) 기관 및 해당 파트너를 사용 하 여 중요 업무용 워크 로드를 클라우드로 변환할 수 있는 전용 클라우드를 제공 합니다.  

이 문서에서는 Azure Government의 Azure NetApp Files 기능 가용성에 대해 설명 합니다. 또한 Azure Government 내의 Azure NetApp Files 서비스에 액세스 하는 방법을 보여 줍니다.

## <a name="feature-availability"></a>기능 가용성

Azure NetApp Files에서 지 원하는 Azure Government 지역에 대해서는 *[지역별 사용 가능한 제품 페이지](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)* 를 참조 하세요.  

***다음 표에 나열 된 기능을 제외 하 고*** Azure 공용 클라우드에서 제공 되는 모든 [Azure NetApp Files 기능](whats-new.md) 을 지원 되는 Azure Government 지역 에서도 사용할 수 있습니다. 

| Azure NetApp Files 기능 | Azure 공용 클라우드 가용성 |  Azure Government 가용성 |
|:--- |:--- |:--- |
| 지역 간 복제 Azure NetApp Files | GA(일반 공급) | [제한적](cross-region-replication-introduction.md#supported-region-pairs) |
| Azure NetApp Files 백업 | 퍼블릭 미리 보기 | No |

## <a name="portal-access"></a>포털 액세스

Azure Government 사용자는 브라우저를 **portal.azure.us** 에 연결 하 여 Azure NetApp Files에 액세스할 수 있습니다.포털 사이트 이름은 **Microsoft Azure 정부** 입니다.자세한 내용은 [포털을 사용 하 여 Azure Government 커넥트을](../azure-government/documentation-government-get-started-connect-with-portal.md) 참조 하세요.   

![URL로 portal.azure.us를 강조 표시 하는 Azure Government 포털의 스크린샷](../media/azure-netapp-files/azure-government.jpg)

Microsoft Azure 정부 포털에서 Azure Portal 하는 것과 동일한 방식으로 Azure NetApp Files에 액세스할 수 있습니다.예를 들어 포털의 리소스 검색 상자에 **Azure NetApp Files** 를 입력 한 다음 표시 되는 목록에서 **Azure NetApp Files** 을 선택할 수 있습니다.  

서비스 사용에 대 한 자세한 내용은 [Azure NetApp Files](./index.yml) 설명서를 참조 하세요.

## <a name="azure-cli-access"></a>Azure CLI 액세스

클라우드 이름을로 설정 하 여 Azure Government에 연결한 `AzureUSGovernment` 다음 명령을 사용 하 여 평소와 같이 로그인을 계속 진행할 수 있습니다 `az login` . 로그인 명령을 실행 한 후에는 적절 한 Azure Government 자격 증명을 입력 하는 브라우저가 시작 됩니다.  

```azurecli 

az cloud set --name AzureUSGovernment 

``` 

클라우드가로 설정 되었는지 확인 하려면 `AzureUSGovernment` 다음을 실행 합니다. 

```azurecli 

az cloud list --output table 

``` 

이 명령을 사용 하면 Azure 클라우드 위치가 있는 테이블이 생성 됩니다. `isActive`의 열 항목을 `AzureUSGovernment` 읽어야 `true` 합니다.  

자세한 내용은 [Azure CLI를 사용 하 여 Azure Government 커넥트을](../azure-government/documentation-government-get-started-connect-with-cli.md) 참조 하세요.

## <a name="rest-api-access"></a>REST API 액세스

Azure Government 끝점은 상용 Azure 끝점과 다릅니다. 다른 끝점의 목록은 [개발자를 위한](../azure-government/compare-azure-government-global-azure.md#guidance-for-developers)Azure Government의 지침을 참조 하세요.

## <a name="powershell-access"></a>PowerShell 액세스

PowerShell을 통해 Azure Government에 연결 하는 경우 올바른 끝점에 연결할 수 있도록 환경 매개 변수를 지정 해야 합니다. 여기서는 PowerShell에서와 같이 Azure NetApp Files 사용을 계속할 수 있습니다. 

| 연결 형식 | 명령 | 
| --- | --- | 
| [Azure](/powershell/module/az.accounts/Connect-AzAccount) 명령 |`Connect-AzAccount -EnvironmentName AzureUSGovernment` | 
| [Azure Active Directory](/powershell/module/azuread/connect-azuread) 명령 |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` | 
| [Azure (클래식 배포 모델)](/powershell/module/servicemanagement/azure.service/add-azureaccount) 명령 |`Add-AzureAccount -Environment AzureUSGovernment` | 
| [Azure Active Directory (클래식 배포 모델)](/previous-versions/azure/jj151815(v=azure.100)) 명령 |`Connect-MsolService -AzureEnvironment UsGovernment` | 

자세한 내용은 [PowerShell을 사용 하 여 Azure Government 커넥트을](../azure-government/documentation-government-get-started-connect-with-ps.md) 참조 하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Government란?](../azure-government/documentation-government-welcome.md)
* [Azure NetApp Files의 새로운 기능](whats-new.md)
* [Azure Government와 글로벌 Azure 비교](../azure-government/compare-azure-government-global-azure.md)
* [Azure NetApp Files REST API](azure-netapp-files-develop-with-rest-api.md)
* [PowerShell을 사용 하 여 Azure NetApp Files REST API](develop-rest-api-powershell.md)