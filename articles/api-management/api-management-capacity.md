---
title: Azure API Management 인스턴스의 용량 | Microsoft Docs
description: 이 문서에서는 용량 메트릭이 무엇인지, 그리고 정보에 근거하여 Azure API Management 인스턴스를 확장할지 여부를 결정하는 방법을 설명합니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: c7c002b57f2220ac0a9fba43a8081b2a4ed800e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93081062"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Azure API Management 인스턴스의 용량

**용량** 은 부하를 더 많이 수용하기 위해 정보에 근거하여 API Management 인스턴스를 확장할지 여부에 대한 결정을 내리는 데 가장 중요한 [Azure Monitor 메트릭](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis)입니다. 이러한 구성은 복잡하며 특정 동작을 적용합니다.

이 문서에서는 **용량** 이 무엇이며 어떻게 동작하는지에 대해 설명합니다. Azure Portal에서 **용량** 메트릭에 액세스하는 방법을 보여주고 API Management 인스턴스에 대한 확장이나 업그레이드를 고려할 시기를 제안합니다.

> [!IMPORTANT]
> 이 문서에서는 용량 메트릭에 따라 Azure API Management 인스턴스를 모니터링하고 크기 조정하는 방법을 설명합니다. 그러나 개별 API Management 인스턴스가 실제로 용량에 *도달* 했을 때 어떤 일이 발생하는지 이해하는 것도 마찬가지로 중요합니다. Azure API Management는 인스턴스의 실제 오버로드를 방지하기 위해 서비스 수준 제한을 적용하지 않습니다. 인스턴스가 실제 용량에 도달하면 들어오는 요청을 처리할 수 없는 오버로드된 웹 서버와 비슷하게 동작합니다. 대기 시간이 증가하고 연결이 삭제되며 시간 제한 오류가 발생합니다. 즉, API 클라이언트는 다른 외부 서비스와 유사하게(예: 다시 시도 정책을 적용하여) 이 가능성에 대처할 수 있도록 준비해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 따르려면 다음이 필요합니다.

+ 활성화된 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>용량이란?

![용량 메트릭에 대해 설명하는 다이어그램입니다.](./media/api-management-capacity/capacity-ingredients.png)

**용량** 은 API Management 인스턴스의 로드를 나타내는 지표입니다. 여기에는 리소스 사용량(CPU, 메모리)과 네트워크 큐 길이가 반영됩니다. CPU와 메모리 사용량은 다음을 통해 리소스 사용량을 나타냅니다.

+ 요청 처리와 같은 API Management 데이터 평면 서비스(요청 전달 또는 정책 실행을 포함할 수 있음)
+ Azure Portal 또는 ARM을 통해 적용되는 관리 작업 또는 [개발자 포털](api-management-howto-developer-portal.md)에서 오는 로드와 같은 API Management 관리 평면 서비스
+ 선택된 운영 체제 프로세스(새 연결에서의 TLS 핸드셰이크 비용을 포함하는 프로세스 포함)

총 **용량** 은 API Management 인스턴스의 모든 단위에서 가져온 자체 값의 평균입니다.

**용량 메트릭** 은 API Management 인스턴스의 문제를 표시하도록 설계되었지만 문제가 **용량 메트릭** 의 변경 사항에 반영되지 않는 경우가 있습니다.

## <a name="capacity-metric-behavior"></a>용량 메트릭 동작

용량의 구문 때문에, 실제 **용량** 은 많은 변수의 영향을 받을 수 있습니다. 예를 들면:

+ 연결 패턴(요청 시 새 연결 생성 및 기존 연결 재사용)
+ 요청 및 응답의 크기
+ 요청을 보내는 각 API 또는 클라이언트 수에 관해 구성된 정책

요청에 대한 작업이 복잡할수록 **용량** 소비는 더 많아집니다. 예를 들어, 복잡한 변환 정책은 간단한 요청 전달보다 훨씬 많은 CPU를 소비합니다. 느린 백 엔드 서비스 응답도 이것을 증가시킵니다.

> [!IMPORTANT]
> **용량** 은 처리 중인 요청 수에 대한 직접 측정값이 아닙니다.

![용량 메트릭 스파이크](./media/api-management-capacity/capacity-spikes.png)

**용량** 은 처리 중인 요청이 없어도 간헐적으로 스파이크가 발생하거나 0보다 커질 수 있습니다. 이런 현상은 시스템 또는 플랫폼 전용 작업으로 인해 발생하며 인스턴스 확장 여부를 결정할 때 고려하지 않아야 합니다.

**용량 메트릭** 이 낮다고 해서 무조건 API Management 인스턴스에 문제가 발생하지 않는다는 의미는 아닙니다.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Azure Portal을 사용하여 용량 검사
  
![용량 메트릭](./media/api-management-capacity/capacity-metric.png)  

1. [Azure Portal](https://portal.azure.com/)에서 APIM 인스턴스로 이동합니다.
2. **메트릭** 을 선택합니다.
3. 자주색 섹션의 사용 가능한 메트릭에서 **용량** 메트릭을 선택하고 기본 **평균** 집계는 그대로 유지합니다.

    > [!TIP]
    > 잘못된 해석을 피하기 위해 항상 위치별 **용량** 메트릭 분석을 확인해야 합니다.

4. 녹색 섹션에서 메트릭을 차원으로 분할할 **위치** 를 선택합니다.
5. 섹션 위쪽의 막대에서 원하는 시간 범위를 선택합니다.

    예상치 못한 상황이 발생할 때 알려주도록 메트릭 경고를 설정할 수 있습니다. 예를 들어 APIM 인스턴스가 예상되는 최대 용량을 20분 이상 초과하는 경우 알림을 받습니다.

    >[!TIP]
    > 서비스 용량이 부족한 경우 알리도록 알림을 구성하거나 Azure Monitor 자동 크기 조정 기능을 사용하여 Azure API Management 단위를 자동으로 추가할 수 있습니다. 크기 조정 작업에는 30분 정도가 소요될 수 있으니 여기에 맞게 규칙을 계획해야 합니다.  
    > 마스터 위치만 크기 조정이 허용됩니다.

## <a name="use-capacity-for-scaling-decisions"></a>용량을 사용하여 크기 조정 결정

**용량** 은 부하를 더 많이 수용하기 위해 정보에 근거하여 API Management 인스턴스를 확장할지 여부에 대한 결정을 내리기 위한 메트릭입니다. 고려할 사항은 다음과 같습니다.

+ 장기 추세와 평균을 살펴봅니다.
+ 부하 증가와 관련이 없는 급격한 스파이크는 무시합니다("용량 메트릭 동작" 섹션에서 자세한 내용 참조).
+ 긴 시간(예: 30분)동안 **용량** 의 값이 60%나 70%를 초과하는 경우 인스턴스를 업그레이드하거나 크기를 조정합니다. 서비스 또는 시나리오에 따라 다른 값이 더 적당할 수 있습니다.

>[!TIP]  
> 미리 트래픽을 예측할 수 있으면 APIM 인스턴스를 예상 워크로드에서 테스트합니다. 테넌트에서 요청 로드를 점차적으로 늘리고 최대 부하에 해당하는 용량 메트릭의 값이 무엇인지 모니터링합니다. Azure Portal을 사용하여 특정 시간에 얼마나 많은 용량이 사용되는지 이해하려면 이전 섹션의 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계

- [Azure API Management 서비스 인스턴스의 크기를 조정하거나 업그레이드하는 방법](upgrade-and-scale.md)
- [클라우드 비용 최적화 및 비용 절약](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)