---
title: Azure Monitor의 데이터 수집 규칙
description: 콘텐츠 및 구조체를 비롯한 Azure Monitor에서의 DCR(데이터 수집 규칙) 개요와 이를 만들고 사용하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.custom: references_region
ms.openlocfilehash: 7cd41389384ddc940b4d4bb2f73b5ccf50a80c77
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133539252"
---
# <a name="data-collection-rules-in-azure-monitor"></a>Azure Monitor의 데이터 수집 규칙
DCR(데이터 수집 규칙)은 Azure Monitor에 들어오는 데이터를 정의하고 데이터를 보내거나 저장할 위치를 지정합니다. 이 문서에서는 콘텐츠 및 구조체를 포함한 데이터 수집 규칙의 개요와 이를 만들고 사용하는 방법을 설명합니다.

## <a name="input-sources"></a>입력 원본
데이터 수집 규칙은 현재 다음과 같은 입력 원본을 지원합니다.

- 가상 머신, 가상 머신 확장 집합 및 서버용 Azure Arc에서 실행되는 Azure Monitor 에이전트. [Azure Monitor 에이전트를 위한 데이터 수집 구성(미리 보기)](../agents/data-collection-rule-azure-monitor-agent.md)을 참조하세요.



## <a name="components-of-a-data-collection-rule"></a>데이터 수집 규칙의 구성 요소
데이터 수집 규칙에는 다음 구성 요소가 포함됩니다.

| 구성 요소 | Description |
|:---|:---|
| 데이터 원본 | 자체 형식 및 해당 데이터를 노출하는 메서드를 사용하는 모니터링 데이터의 고유한 원본. 데이터 원본의 예로는 Windows 이벤트 로그, 성능 카운터 및 syslog가 있습니다. 각 데이터 원본은 아래에 설명된 것처럼 특정 데이터 원본 형식과 일치합니다. |
| 스트림 | 한 가지 형식으로 변환 및 스키마된 데이터 원본 집합을 설명하는 고유 핸들입니다. 각 데이터 원본에는 하나 이상의 스트림이 필요하고, 여러 데이터 원본에서 하나의 스트림을 사용할 수 있습니다. 한 스트림의 모든 데이터 원본은 공통 스키마를 공유합니다. 예를 들어 동일한 Log Analytics 작업 영역에 있는 여러 테이블에 특정 데이터 원본을 보내려는 경우 여러 스트림을 사용합니다. |
| 대상 | 데이터를 전송해야 하는 대상 집합입니다. 예를 들면 Log Analytics 작업 영역 및 Azure Monitor 메트릭이 있습니다. | 
| 데이터 흐름 | 대상에 보내야 하는 스트림을 정의합니다. | 

데이터 수집 규칙은 지역적으로 저장되며 Log Analytics가 지원되는 모든 공개 지역에서 사용할 수 있습니다. 현재 정부 지역 및 클라우드는 지원되지 않습니다.

다음 다이어그램에서는 데이터 수집 규칙의 구성 요소와 그 관계를 설명합니다

[![DCR 다이어그램](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>데이터 원본 유형
각 데이터 원본에는 데이터 원본 형식이 있습니다. 각 형식은 각 데이터 원본에 대해 지정해야 하는 고유한 속성 집합을 정의합니다. 현재 사용할 수 있는 데이터 원본 형식은 다음 표에 나와 있습니다.

| 데이터 원본 유형 | Description | 
|:---|:---|
| 확장 | VM 확장 기반 데이터 원본 |
| performanceCounters | Windows 및 Linux용 성능 카운터 |
| syslog | Linux의 Syslog 이벤트 |
| windowsEventLogs | Windows 이벤트 로그 |


## <a name="limits"></a>제한
각 데이터 수집 규칙에 적용되는 제한에 대해서는 [Azure Monitor 서비스 제한](../service-limits.md#data-collection-rules)을 참조하세요.

## <a name="data-resiliency-and-high-availability"></a>데이터 복원력 및 고가용성
서비스의 데이터 수집 규칙은 분산되어 배포됩니다. 사용자가 지정하는 지역에 규칙이 생성되어 저장되고 동일한 지역 내에서 [쌍을 이루는 지역](../../availability-zones/cross-region-replication-azure.md#azure-cross-region-replication-pairings-for-all-geographies)에 백업됩니다.  
추가로 서비스는 지역 내 3개의 [가용성 영역](../../availability-zones/az-overview.md#availability-zones) 모두에 배포되어 고가용성을 추가로 더하는 **영역 중복 서비스** 가 됩니다.


**단일 지역 데이터 상주**: 단일 지역에 고객 데이터를 저장할 수 있는 기능은 현재 아시아 태평양 지역의 동남 아시아 지역(싱가포르) 및 브라질 지역의 브라질 남부(상파울루 주)에서만 사용할 수 있습니다. 이러한 지역에서는 단일 지역 상주가 기본적으로 사용하도록 설정되어 있습니다.

## <a name="create-a-dcr"></a>DCR 만들기
다음 중 원하는 방법을 사용하여 DCR을 만들 수 있습니다.

- [Azure Portal을 사용](../agents/data-collection-rule-azure-monitor-agent.md)하여 데이터 수집 규칙을 만들고 하나 이상의 가상 머신과 연결합니다.
- JSON에서 데이터 수집 규칙을 직접 편집하고 [REST API를 사용하여 제출](/rest/api/monitor/datacollectionrules)합니다.
- DCR 및 연결을 [Azure CLI](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md)를 사용해 만듭니다.
- Azure PowerShell을 사용하여 DCR 및 연결을 만듭니다.
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Update-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>샘플 데이터 수집 규칙
아래의 샘플 데이터 수집 규칙은 Azure Monitor 에이전트를 사용하는 가상 머신에 대한 것이며 다음과 같은 세부 정보를 포함합니다.

- 성능 데이터
  - 15초마다 특정 프로세서, 메모리, 논리 디스크 및 실물 디스크 카운터를 수집하고 1분마다 업로드합니다.
  - 30초마다 특정 프로세스 카운터를 수집하고 5분마다 업로드합니다.
- Windows 이벤트
  - 1분마다 Windows 보안 이벤트 및 업로드를 수집합니다.
  - Windows 애플리케이션 및 시스템 이벤트를 수집하고 5분마다 업로드합니다.
- Syslog
  - cron 시설에서 디버그, 위험 및 긴급 이벤트를 수집합니다.
  - Syslog 시설에서 경고, 위험 및 긴급 이벤트를 수집합니다.
- 대상
  - centralWorkspace라는 Log Analytics 작업 영역에 모든 데이터를 보냅니다.

> [!NOTE]
> 데이터 수집 규칙에서 이벤트 수집을 지정하는 데 사용되는 XPath에 대한 설명은 다음을 참조하세요. [사용자 지정 XPath 쿼리를 사용하여 데이터 컬렉션 제한](data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)


```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>다음 단계

- [데이터 수집 규칙](data-collection-rule-azure-monitor-agent.md) 및 연결을 Azure Monitor 에이전트를 사용하여 가상 머신에서 만듭니다.
