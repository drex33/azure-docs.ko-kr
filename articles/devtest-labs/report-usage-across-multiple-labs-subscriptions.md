---
title: 여러 랩 및 구독에서의 Azure DevTest Labs 사용량
description: 여러 랩 및 구독에서 Azure DevTest Labs 사용량을 보고하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: d2bf6954b629c3a9fc28569a86df4aa61f5dc94f
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401044"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>여러 랩 및 구독에서의 Azure DevTest Labs 사용량 보고

대부분의 큰 조직은 추세와 이상 값을 시각화 하는 데 보다 효과적으로 리소스 사용량을 추적 하려고 합니다. 리소스 사용량을 기반으로 랩 소유자 또는 관리자는 랩을 사용자 지정 하 여 [리소스 사용량 및 비용을 향상할](../cost-management-billing/cost-management-billing-overview.md)수 있습니다. Azure DevTest Labs에서 랩 당 리소스 사용량을 다운로드 하 여 사용 패턴에 대 한 세부적인 기록을 볼 수 있습니다. 이러한 사용 패턴은 효율성 향상을 위해 변경 사항을 정확히 파악 하는 데 도움이 됩니다. 대부분의 기업은 [여러 랩 및 구독](/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 전반에서 개별 랩 사용량과 전체 사용량을 모두 확인하고자 합니다. 

이 문서에서는 여러 랩 및 구독에서의 리소스 사용량 정보를 처리하는 방법을 설명합니다.

![보고서 사용량](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>개별 랩 사용량

이 섹션에서는 단일 랩에 대한 리소스 사용량을 내보내는 방법을 설명합니다.

DevTest Labs 리소스 사용을 내보내려면 먼저 사용 데이터를 포함 하는 파일에 대 한 Azure Storage 계정을 설정 해야 합니다. 데이터 내보내기를 실행 하는 두 가지 일반적인 방법은 다음과 같습니다.

* [DevTest Labs REST API](/rest/api/dtl/labs/exportresourceusage) 
* `exportResourceUsage` 동작, 랩 리소스 ID, 필요한 매개 변수가 있는 PowerShell Az.Resource 모듈 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction). 

    [개인 데이터 내보내기 또는 삭제](personal-data-delete-export.md) 문서에는 내보낸 데이터에 대한 자세한 정보가 있는 샘플 PowerShell 스크립트가 포함되어 있습니다. 

    > [!NOTE]
    > 날짜 매개 변수는 타임스탬프를 포함하지 않으므로 랩이 위치한 표준 시간대를 기준으로 자정부터 모든 항목이 데이터에 포함됩니다.

내보내기가 완료되면 Blob 스토리지에 다양한 리소스 정보가 포함된 여러 CSV 파일이 만들어집니다.
  
현재는 다음 두 개의 CSV 파일이 있습니다.

* *virtualmachines.csv* - 랩의 가상 머신에 관한 정보 포함
* *disks.csv* - 랩의 다른 디스크에 대한 정보 포함 

이러한 파일은 *lab resourceusage* blob 컨테이너에 저장 됩니다. 파일은 랩 이름, 랩 고유 ID, 실행 날짜 및 `full` 내보내기 요청의 시작 날짜에 있습니다. 예제 blob 구조는 다음과 같습니다.

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>모든 랩에 대한 사용량 내보내기

여러 랩에 대 한 사용 정보를 내보내려면 다음을 사용 하십시오. 

* PowerShell을 비롯한 여러 언어에서 사용할 수 있는 [Azure Functions](../azure-functions/index.yml), 
* [Azure Automation Runbook](../automation/index.yml), PowerShell 또는 Python을 사용하거나 사용자 지정 그래픽 디자이너를 사용하여 내보내기 코드를 작성할 수 있습니다.

해당 기술을 사용하여 특정 날짜 및 시간에 모든 랩에서 개별 랩 내보내기를 실행할 수 있습니다. 

Azure 함수는 데이터를 장기 스토리지로 푸시해야 합니다. 여러 랩에 대 한 데이터를 내보내는 경우 내보내기에 시간이 걸릴 수 있습니다. 성능을 지원 하 고 정보의 중복 가능성을 줄이려면 각 랩을 병렬로 실행 하는 것이 좋습니다. 병렬 처리를 수행하려면 Azure Functions를 비동기로 실행합니다. 또한 Azure Functions 제공 하는 타이머 트리거를 활용 합니다.

## <a name="using-a-long-term-storage"></a>장기 스토리지 사용하기

장기 스토리지는 여러 랩의 내보내기 정보를 단일 데이터 원본으로 통합합니다. 또한 장기 스토리지를 사용하면 스토리지 계정에서 파일을 제거하여 중복 및 비용을 줄일 수 있다는 장점이 있습니다. 

장기 스토리지를 사용하여 텍스트 조작을 수행할 수도 있습니다. 예를 들면 다음과 같습니다. 

* 이름 추가
* 복합 그룹 만들기
* 데이터 집계

몇 가지 일반적인 스토리지 솔루션은 [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/), [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)입니다. 선택한 장기 저장소 솔루션은 기본 설정에 따라 달라 집니다. 데이터를 시각화할 때 상호 작용 가용성을 위해 제공 하는 기능에 따라 도구를 선택 하는 것을 고려할 수 있습니다.

## <a name="visualizing-data-and-gathering-insights"></a>데이터 시각화 및 인사이트 수집

원하는 데이터 시각화 도구를 사용하여 사용량 데이터를 표시하고 정보를 수집하여 사용 효율성을 확인합니다. 예를 들어 [Power BI](/power-bi/power-bi-overview)를 사용하여 사용량 데이터를 구성하고 표시할 수 있습니다. 

[Azure Data Factory](https://azure.microsoft.com/services/data-factory/)를 사용하여 단일 위치 인터페이스 내에서 리소스를 만들고, 연결하고, 관리할 수 있습니다. 더 많은 제어가 필요한 경우 개별 리소스를 단일 리소스 그룹 내에 만들고 Data Factory 서비스와 독립적으로 관리할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

시스템을 설정 하 고 데이터가 장기 저장소로 이동 하는 경우 다음 단계는 데이터에 응답 해야 하는 질문을 표시 하는 것입니다. 예를 들면 다음과 같습니다. 

-   VM 크기 사용량은 얼마인가요?

    사용자가 고성능(더 비싼) VM 크기를 선택하나요?
-   어떤 마켓플레이스 이미지가 사용되고 있나요?

    사용자 지정 이미지가 가장 일반적인 VM 기반이며, [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) 또는 [이미지 팩터리](image-factory-create.md)와 같은 방식으로 이미지 스토리지를 구축해야 하나요?
-   어떤 사용자 지정 이미지가 사용되고 어떤 것이 사용되지 않나요?