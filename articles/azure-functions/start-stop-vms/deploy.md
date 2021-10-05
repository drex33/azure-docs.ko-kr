---
title: VM v2 시작/중지(미리 보기) 배포
description: 이 문서에서는 Azure 구독에서 Azure VM에 대한 VM v2 시작/중지(미리 보기) 기능을 배포하는 방법을 설명합니다.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: dc44fc58b8832c30ed1b740eb6637f1fc8ed1413
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129454965"
---
# <a name="deploy-startstop-vms-v2-preview"></a>VM v2 시작/중지(미리 보기) 배포

VM v2 시작/중지(미리 보기) 기능을 설치하려면 순서대로 이 항목의 단계를 수행합니다. 설치 프로세스를 완료한 후 일정을 구성하여 요구 사항에 맞게 사용자 지정합니다.

## <a name="permissions-considerations"></a>사용 권한 고려 사항
배포 전과 배포 중에 다음에 유의하세요.
+   이 솔루션을 사용하면 v2 시작/중지 배포에 대한 적절한 RBAC(역할 기반 액세스 제어) 권한이 있는 사용자가 v2 시작/중지 범위에서 가상 머신에 대한 일정을 추가, 제거 및 관리할 수 있습니다. 이 동작은 의도된 것입니다. 실제로 가상 머신에 대한 직접 RBAC 권한이 없는 사용자는 V2 시작/중지 솔루션을 수정할 수 있는 RBAC 권한이 있는 경우 해당 가상 머신에서 시작, 중지 및 자동 중지 작업을 만들 수 있습니다.
+ v2 시작/중지 솔루션에 액세스할 수 있는 모든 사용자는 비용, 절약, 작업 기록 및 v2 시작/중지 애플리케이션에서 사용하는 Application Insights 인스턴스에 저장된 기타 데이터를 확인할 수 있습니다.
+ v2 시작/중지 솔루션을 관리할 때는 특히 대상 가상 머신을 직접 수정할 수 있는 권한이 없는 경우 v2 시작/중지 솔루션에 대한 사용자의 권한을 고려해야 합니다.
## <a name="deploy-feature"></a>배포 기능

배포는 [여기](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)에서 VM v2 시작/중지 GitHub 조직에서 시작됩니다. 이 기능은 구독 내에서 단일 배포의 모든 리소스 그룹에서 구독의 모든 VM을 관리하기 위한 것이지만 조직의 운영 모델 또는 요구 사항에 따라 다른 인스턴스를 설치할 수 있습니다. 또한 여러 구독에서 VM을 중앙에서 관리하도록 구성할 수 있습니다.

관리 및 제거를 간소화하려면 전용 리소스 그룹에 VM v2 시작/중지(미리 보기)를 배포하는 것이 좋습니다.

> [!NOTE]
> 현재 이 미리 보기는 기존 스토리지 계정 또는 Application Insights 리소스를 지정하는 것을 지원하지 않습니다.


> [!NOTE]
> 함수 앱 및 스토리지 계정에 대한 명명 형식이 변경되었습니다. 글로벌 고유성을 보장하기 위해 이제 이러한 리소스의 이름에 임의 및 고유 문자열이 추가됩니다.

1. 브라우저를 열고 VM v2 시작/중지 [GitHub 조직](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)으로 이동합니다.
1. Azure VM이 생성되는 Azure 클라우드 환경을 기반으로 배포 옵션을 선택합니다. 이렇게 하면 Azure Portal의 사용자 지정 Azure Resource Manager 배포 페이지가 열립니다.
1. 메시지가 표시되면 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 다음 값을 입력합니다.

    |Name |값 |
    |-----|------|
    |지역 |새 리소스에 대해 가까운 지역의 지역을 선택합니다.|
    |리소스 그룹 이름 |VM 시작/중지에 대한 개별 리소스를 포함할 리소스 그룹 이름을 지정합니다. |
    |리소스 그룹 지역 |리소스 그룹에 대한 영역을 지정합니다. 예: **미국 중부**|
    |Azure 함수 앱 이름 |URL 경로에 유효한 이름을 입력합니다. 입력한 이름이 Azure Functions에서 고유한지 확인하기 위해 유효성을 검사합니다. |
    |Application Insights 이름 |VM 시작/중지에 대한 분석을 보유할 Application Insights 인스턴스의 이름을 지정합니다. |
    |Application Insights 지역 |Application Insights 인스턴스의 지역을 지정합니다.|
    |스토리지 계정 이름 |VM 시작/중지 실행 원격 분석을 저장할 Azure Storage 계정의 이름을 지정합니다. |
    |메일 주소 |상태 알림을 받을 하나 이상의 이메일 주소를 쉼표(,)로 구분하여 지정합니다.|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="VM 시작/중지 템플릿 배포 구성":::

1. 페이지 하단에서 **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택하여 배포를 시작합니다.
1. 화면 위쪽에서 벨 아이콘(알림)을 선택하여 배포 상태를 확인합니다. **배포 진행 중** 이 표시됩니다. 배포가 완료될 때까지 기다립니다.
1. 알림 창에서 **리소스 그룹으로 이동** 을 선택합니다. 다음과 비슷한 화면이 표시됩니다.

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="VM 시작/중지 템플릿 배포 리소스 목록":::

> [!NOTE]
> 문제 해결을 위해 지원 팀에 문의하는 경우 사용자에게 더 나은 도움을 주기 위해 작업 및 하트비트 원격 분석을 수집하고 있습니다. 또한 가상 머신 이벤트 기록을 수집하여 서비스가 가상 머신에서 작동한 시기와 서비스의 효율성을 확인하기 위해 가상 머신이 다시 알림을 받은 기간도 확인합니다.

## <a name="enable-multiple-subscriptions"></a>여러 구독 사용

배포 시작/중지를 완료한 후에는 다음 단계를 수행하여 VM v2 시작/중지(미리 보기)에서 여러 구독에 대한 작업을 수행하도록 설정합니다.

1. 배포하는 동안 지정한 Azure 함수 앱 이름에 대한 값을 복사합니다.

1. 포털에서 보조 구독으로 이동합니다. 구독을 선택하고 **액세스 제어(IAM)** 를 선택합니다.

1. **추가** 를 선택한 다음 **역할 할당 추가** 를 선택합니다.

1. **역할** 드롭다운 목록에서 **기여자** 역할을 선택합니다.

1. **선택** 필드에 Azure Function 애플리케이션 이름을 입력합니다. 결과에서 함수 이름을 선택합니다.

1. 변경 내용을 적용하려면 **저장** 을 선택합니다.

## <a name="configure-schedules-overview"></a>일정 구성 개요

VM의 시작 및 중지를 제어하는 자동화 방법을 관리하려면 요구 사항에 따라 포함된 논리 앱을 하나 이상 구성합니다.

- 예약됨 - 시작 및 중지 작업은 Azure Resource Manager 및 클래식 VM에 대해 지정한 일정을 기반으로 합니다. **ststv2_vms_Scheduled_start** 및 **ststv2_vms_Scheduled_stop** 은 예약된 시작 및 중지를 구성합니다.

- Sequenced - 시작 및 중지 작업은 미리 정의된 시퀀싱 태그를 사용하여 VM을 대상으로 하는 일정을 기반으로 합니다. 두 개의 명명된 태그(**sequencestart** 및 **sequencestop**)만 지원됩니다. **ststv2_vms_Sequenced_start** 및 **ststv2_vms_Sequenced_stop** 은 시퀀싱된 시작 및 중지를 구성합니다.

    > [!NOTE]
    > 이 시나리오는 Azure Resource Manager VM만 지원합니다.

- AutoStop - 이 기능은 CPU 사용률을 기반으로 Azure Resource Manager 및 클래식 VM 모두에 대해 중지 동작을 수행하는 데만 사용됩니다. VM에 경고를 생성하고 조건에 따라 경고가 트리거되어 중지 작업을 수행하는 예약 기반 *작업 수행* 일 수도 있습니다. **ststv2_vms_AutoStop** 은 자동 중지 기능을 구성합니다.

추가 일정이 필요한 경우 Azure Portal에서 **복제** 옵션을 사용하여 제공된 Logic Apps 중 하나를 복제할 수 있습니다.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="논리 앱을 복제하려면 복제 옵션을 선택합니다.":::

## <a name="scheduled-start-and-stop-scenario"></a>예약된 시작 및 중지 시나리오

Azure Resource Manager 및 클래식 VM에 대한 예약된 시작 및 중지 작업을 구성하려면 다음 단계를 수행합니다. 예를 들어 사무실에 있을 때 아침에 시작하도록 **ststv2_vms_Scheduled_start** 일정을 구성할 수 있으며, **ststv2_vms_Scheduled_stop** 일정에 따라 저녁에 퇴근할 때 구독 전체의 모든 VM을 중지할 수 있습니다.

VM을 시작하도록 논리 앱을 구성하는 것이 지원됩니다.

각 시나리오에 대해 하나 이상의 구독, 단일 또는 여러 리소스 그룹에 대해 작업 대상을 지정하고 포함 또는 제외 목록에서 하나 이상의 VM을 지정할 수 있습니다. 동일한 논리 앱에서 함께 지정할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음 **논리 앱** 으로 이동합니다.

1. 논리 앱 목록에서 예약된 시작을 구성하려면 **ststv2_vms_Scheduled_start** 를 선택합니다. 예약된 중지를 구성하려면 **ststv2_vms_Scheduled_stop** 을 선택합니다.

1. 왼쪽 창에서 **논리 앱 디자이너** 를 선택합니다.

1. 논리 앱 디자이너가 나타나면 디자이너 창에서 **반복** 을 선택하여 논리 앱 일정을 구성합니다. 특정 반복 옵션에 대한 자세한 내용은 [반복 작업 예약](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)을 참조하세요.

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="논리 앱의 반복 빈도 구성":::

1. 디자이너 창에서 **기능 시도** 를 선택하여 대상 설정을 구성합니다. 요청 본문에서 구독의 모든 리소스 그룹에서 VM을 관리하려는 경우 다음 예와 같이 요청 본문을 수정합니다.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    다음 예와 같이 쉼표로 구분된 각 값을 사용하여 `subscriptions` 배열에 여러 구독을 지정합니다.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    요청 본문에서 특정 리소스 그룹에 대한 VM을 관리하려는 경우 다음 예와 같이 요청 본문을 수정합니다. 지정된 각 리소스 경로는 쉼표로 구분해야 합니다. 필요한 경우 리소스 그룹을 하나 이상 지정할 수 있습니다.

    이 예에서는 가상 머신을 제외하는 방법도 보여 줍니다. VM 리소스 경로를 지정하거나 와일드카드를 지정하여 VM을 제외할 수 있습니다.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    여기에서 VM 이름을 제외하고 모든 VM에 대해 수행되는 작업은 두 구독 모두에서 Az 및 Bz로 시작합니다.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    요청 본문에서 구독 내의 특정 VM 집합을 관리하려는 경우 다음 예와 같이 요청 본문을 수정합니다. 지정된 각 리소스 경로는 쉼표로 구분해야 합니다. 필요한 경우 VM 하나를 지정할 수 있습니다.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
      }
    }
    ```

1. 논리 앱에 대 한 개요 창에서 **사용** 을 선택 합니다.  

## <a name="sequenced-start-and-stop-scenario"></a>순차화된 시작 및 중지 시나리오

분산 애플리케이션 아키텍처에서 여러 Azure Resource Manager VM에 둘 이상의 구성 요소를 포함하는 환경에서 구성 요소가 시작되고 중지되는 순서를 지원하는 것이 중요합니다.

1. 논리 앱 목록에서 순차 시작을 구성하려면 **ststv2_vms_Sequenced_start** 를 선택합니다. 순차 중지를 구성하려면 **ststv2_vms_Sequenced_stop** 을 선택합니다.

1. 왼쪽 창에서 **논리 앱 디자이너** 를 선택합니다.

1. 논리 앱 디자이너가 나타나면 디자이너 창에서 **반복** 을 선택하여 논리 앱 일정을 구성합니다. 특정 반복 옵션에 대한 자세한 내용은 [반복 작업 예약](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)을 참조하세요.

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="논리 앱의 반복 빈도 구성":::

1. 디자이너 창에서 **기능 시도** 를 선택하여 대상 설정을 구성합니다. 요청 본문에서 구독의 모든 리소스 그룹에서 VM을 관리하려는 경우 다음 예와 같이 요청 본문을 수정합니다.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    다음 예와 같이 쉼표로 구분된 각 값을 사용하여 `subscriptions` 배열에 여러 구독을 지정합니다.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    요청 본문에서 특정 리소스 그룹에 대한 VM을 관리하려는 경우 다음 예와 같이 요청 본문을 수정합니다. 지정된 각 리소스 경로는 쉼표로 구분해야 합니다. 필요한 경우 하나의 리소스 그룹을 지정할 수 있습니다.

    또한 이 예에서는 와일드카드를 사용하는 예약된 시작/중지에 대한 예와 비교하여 해당 리소스 경로로 가상 머신을 제외하는 방법을 보여 줍니다.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    요청 본문에서 구독 내의 특정 VM 집합을 관리하려는 경우 다음 예와 같이 요청 본문을 수정합니다. 지정된 각 리소스 경로는 쉼표로 구분해야 합니다. 필요한 경우 VM 하나를 지정할 수 있습니다.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>자동 중지 시나리오

VM v2 시작/중지(미리 보기)는 사용량이 많지 않은 기간(예 : 몇 시간 후) 동안에 사용되지 않는 머신을 평가하여 Azure Resource Manager 및 구독의 클래식 VM 실행에 필요한 비용을 관리하는 데 도움을 줄 수 있습니다. 그리고 프로세서 사용률이 지정된 백분율보다 작은 경우 자동으로 종료하도록 합니다.

요청 본문의 다음 메트릭 경고 속성은 사용자 지정을 지원합니다.

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Azure Monitor 메트릭 경고의 작동 방식 및 구성 방법에 대한 자세한 내용은 [Azure Monitor의 메트릭 경고](../../azure-monitor/alerts/alerts-metric-overview.md)를 참조하세요.

1. 논리 앱 목록에서 자동 중지를 구성하려면 **ststv2_vms_AutoStop** 을 선택합니다.

1. 왼쪽 창에서 **논리 앱 디자이너** 를 선택합니다.

1. 논리 앱 디자이너가 나타나면 디자이너 창에서 **반복** 을 선택하여 논리 앱 일정을 구성합니다. 특정 반복 옵션에 대한 자세한 내용은 [반복 작업 예약](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)을 참조하세요.

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="논리 앱의 반복 빈도 구성":::

1. 디자이너 창에서 **기능 시도** 를 선택하여 대상 설정을 구성합니다. 요청 본문에서 구독의 모든 리소스 그룹에서 VM을 관리하려는 경우 다음 예와 같이 요청 본문을 수정합니다.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    요청 본문에서 특정 리소스 그룹에 대한 VM을 관리하려는 경우 다음 예와 같이 요청 본문을 수정합니다. 지정된 각 리소스 경로는 쉼표로 구분해야 합니다. 필요한 경우 하나의 리소스 그룹을 지정할 수 있습니다.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    요청 본문에서 구독 내의 특정 VM 집합을 관리하려는 경우 다음 예와 같이 요청 본문을 수정합니다. 지정된 각 리소스 경로는 쉼표로 구분해야 합니다. 필요한 경우 VM 하나를 지정할 수 있습니다.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>다음 단계

VM v2 시작/중지(미리 보기) 기능으로 관리되는 Azure VM의 상태를 모니터링하고 기타 관리 작업을 수행하는 방법을 알아보려면 [VM 시작/중지 관리](manage.md) 문서를 참조하세요.
