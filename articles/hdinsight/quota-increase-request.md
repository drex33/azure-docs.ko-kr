---
title: CPU 코어 할당량 증가 요청 - Azure HDInsight
description: 구독에 할당된 CPU 코어 수 증가를 요청하는 프로세스에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: c6a46d1d0224ac802dc2c7b418e1fa19f1b26099
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742863"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Azure HDInsight의 할당량 증가 요청

CPU 코어 할당량은 특정 Azure 지역의 모든 고객 간에 리소스 사용량이 폭넓게 분산되도록 하는 데 도움이 됩니다. 그러나 비즈니스 요구 사항에 따라 현재 할당량보다 더 많은 클러스터 리소스가 필요한 경우가 있을 수 있습니다. 이러한 경우 데이터 처리 요구 사항과 일치하는 클러스터를 배포할 수 있도록 CPU 코어 할당량 증가를 요청할 수 있습니다.

할당량 한도에 도달하면 새 클러스터를 배포하거나, 작업자 노드를 더 추가하여 기존 클러스터를 확장할 수 없습니다. 유일한 할당량 제한은 각 구독에 대한 지역 수준에서 존재하는 CPU 코어 할당량입니다. 예를 들어 미국 동부 지역의 CPU 코어 수가 30개로 제한되고, 미국 동부에서는 또 다른 CPU 코어 30개를 사용할 수 있는 구독이 있을 수 있습니다.

## <a name="gather-required-information"></a>필요한 정보 수집

할당량 한도에 도달했다는 오류 메시지가 표시되면 이 섹션에 설명된 프로세스에 따라 중요한 정보를 수집하고 할당량 증가 요청을 제출합니다.

1. 원하는 클러스터 VM 크기, 규모 및 유형을 결정합니다.
1. 구독의 현재 할당량 용량 한도를 확인합니다. 사용 가능한 코어를 확인하려면 다음 단계를 수행합니다.

    1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
    1. HDInsight 클러스터의 **개요** 페이지로 이동합니다.
    1. 왼쪽 메뉴에서 **할당량 한도** 를 선택합니다. 페이지에 사용 중인 코어 수, 사용 가능한 코어 수 및 총 코어 수가 표시됩니다.

할당량 증가를 요청하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 페이지 왼쪽 맨 아래에서 **도움말 + 지원** 을 선택합니다.

    :::image type="content" source="./media/quota-increase-request/help-support-button.png" alt-text="도움말 및 지원 단추" border="true":::

1. **새 지원 요청** 을 선택합니다.
1. **새 지원 요청** 페이지의 **기본 사항** 탭에서 다음 옵션을 선택합니다.

   - **문제 유형**: **서비스 및 구독 제한(할당량)**
   - **구독**: 수정하려는 구독
   - **할당량 유형**: **HDInsight**

     :::image type="content" source="./media/quota-increase-request/hdinsight-quota-support-request.png" alt-text="HDInsight 코어 할당량을 늘리는 지원 요청 만들기" border="true":::

1. 완료되면 **다음: 솔루션 >>** 을 선택합니다.
1. **세부 정보** 페이지에서 문제에 대한 설명을 입력하고 해당 문제의 심각도, 기본 연락 방법, 기타 필수 필드를 선택합니다. 아래에 나열된 템플릿을 사용하여 필요한 정보를 제공해야 확인합니다. 할당량 증가 요청은 HDInsight 제품 팀이 아닌 Azure 용량 팀에서 평가합니다. 완전한 정보를 제공할수록 요청이 승인될 가능성이 높습니다.

   ```text
   I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
   
   My current quota on this subscription is [CURRENT QUOTA AMOUNT].
   
   I would like to use the extra cores for [DETAIL REASON].
   ```

   :::image type="content" source="./media/quota-increase-request/problem-details.png" alt-text="문제 세부 정보" border="true":::

1. 완료되면 **다음: 검토 + 만들기 >>** 를 선택합니다.
1. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.

> [!NOTE]  
> 프라이빗 영역에서 HDInsight 코어 할당량을 늘려야 하는 경우 [승인 목록에 추가 요청을 제출](https://aka.ms/canaryintwhitelist)합니다.

[지원 서비스에 문의하여 할당량 증가를 요청](../azure-portal/supportability/regional-quota-requests.md)할 수 있습니다.

몇 가지 고정 할당량 한도가 있습니다. 예를 들어 단일 Azure 구독에는 최대 10,000개의 코어가 있을 수 있습니다. 이러한 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Apache Hadoop, Spark, Kafka 등을 사용하여 HDInsight에서 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md): HDInsight에 클러스터를 설치 및 구성하는 방법을 알아봅니다.
* [클러스터 성능 모니터링](hdinsight-key-scenarios-to-monitor.md): 클러스터의 용량에 영향을 줄 수 있는 HDInsight 클러스터를 모니터링하는 주요 시나리오에 대해 알아봅니다.