---
title: Azure Key Vault 경고 구성
description: 주요 자격 증명 모음의 상태를 모니터링 하는 경고를 만드는 방법에 대해 알아봅니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: d74cb93cd28f3dc02b37a6c29832d9e8412ef098
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458911"
---
# <a name="configure-azure-key-vault-alerts"></a>Azure Key Vault 경고 구성

Azure Key Vault 사용 하 여 프로덕션 비밀을 저장 한 후에는 주요 자격 증명 모음의 상태를 모니터링 하 여 서비스가 의도 한 대로 작동 하는지 확인 하는 것이 중요 합니다. 

서비스의 크기를 조정하기 시작하면 키 자격 증명 모음으로 전송되는 요청 수가 증가합니다. 이러한 증가는 요청 대기 시간을 늘릴 수 있습니다. 극단적인 경우 요청을 제한 하 고 서비스의 성능에 영향을 줄 수 있습니다. 키 자격 증명 모음이 비정상적인 수의 오류 코드를 전송 하 고 있는지 여부도 알아야 합니다. 따라서 액세스 정책 또는 방화벽 구성의 문제를 신속 하 게 처리할 수 있습니다. 

이 문서에서는 지정 된 임계값에 따라 경고를 구성 하는 방법을 보여 줍니다. 그러면 주요 자격 증명 모음이 비정상 상태인 경우 팀에서 즉시 조치를 취할 수 있도록 경고할 수 있습니다. 전자 메일을 보내거나 (경우에 맞게 팀 메일 그룹으로), Azure Event Grid 알림을 발생 시키거나, 전화 번호를 통화 하거나 텍스트를 전송 하는 경고를 구성할 수 있습니다. 

다음 경고 유형 중에서 선택할 수 있습니다.

- 고정 값을 기반으로 하는 정적 경고
- 모니터링 되는 메트릭이 정의 된 시간 범위 내에서 특정 횟수의 key vault의 평균 제한을 초과 하는 경우 사용자에 게 알리는 동적 경고입니다.

> [!IMPORTANT]
> 새로 구성 된 경고가 알림 전송을 시작 하는 데 최대 10 분이 소요 될 수 있습니다. 

이 문서에서는 Key Vault에 대 한 경고를 집중적으로 다룹니다. 로그와 메트릭을 모두 결합 하 여 글로벌 모니터링 솔루션을 제공 하는 Key Vault insights에 대 한 자세한 내용은 [Key Vault insights를 사용 하 여 주요 자격 증명 모음 모니터링](../../azure-monitor/insights/key-vault-insights-overview.md#introduction-to-key-vault-insights)을 참조 하세요.

## <a name="configure-an-action-group"></a>작업 그룹 구성 

작업 그룹은 알림 및 속성의 구성 가능한 목록입니다. 경고를 구성 하는 첫 번째 단계는 작업 그룹을 만들고 경고 유형을 선택 하는 것입니다.

1. Azure Portal에 로그인합니다.
2. 검색 상자에서 **경고** 를 검색 합니다.
3. **작업 관리** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![작업 관리 단추를 강조 표시하는 스크린샷.](../media/alert-6.png)

4. **+ 작업 그룹 추가** 를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![작업 그룹을 추가 하는 단추를 강조 표시 하는 스크린샷](../media/alert-7.png)

5. 작업 그룹에 대 한 **작업 유형** 값을 선택 합니다. 이 예제에서는 전자 메일 및 SMS 경고를 만듭니다. **이메일/SMS/푸시/음성** 을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![작업 그룹을 추가 하기 위한 선택 항목을 강조 표시 하는 스크린샷](../media/alert-8.png)

6. 대화 상자에서 전자 메일 및 SMS 세부 정보를 입력 한 다음 **확인** 을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![전자 메일 및 S M 메시지 경고를 추가 하기 위한 선택 항목을 보여 주는 스크린샷](../media/alert-9.png)

## <a name="configure-alert-thresholds"></a>경고 임계값 구성 

다음으로, 규칙을 만들고 경고를 트리거할 임계값을 구성 합니다.

1. Azure Portal에서 키 자격 증명 모음 리소스를 선택한 다음 **모니터링** 아래에서 **경고** 를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![모니터링 섹션에서 경고 메뉴 옵션을 보여 주는 스크린샷](../media/alert-10.png)

2. **새 경고 규칙** 을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![새 경고 규칙을 추가 하는 단추를 보여 주는 스크린샷](../media/alert-11.png)

3. 경고 규칙의 범위를 선택합니다. 단일 자격 증명 모음 또는 여러 자격 증명 모음을 선택할 수 있습니다. 

   > [!IMPORTANT]
   > 경고 범위에 대해 여러 자격 증명 모음을 선택 하는 경우 선택한 모든 자격 증명 모음이 동일한 지역에 있어야 합니다. 다른 지역의 자격 증명 모음에 대해 별도의 경고 규칙을 구성 해야 합니다. 

   > [!div class="mx-imgBorder"]
   > ![자격 증명 모음을 선택할 수 있는 방법을 표시하는 스크린샷.](../media/alert-12.png)

4. 경고에 대 한 논리를 정의 하는 임계값을 선택 하 고 **추가** 를 선택 합니다. Key Vault 팀에서는 다음과 같은 임계값을 구성 하는 것이 좋습니다. 

    + Key Vault 가용성은 100% 아래로 떨어집니다 (정적 임계값).
    + Key Vault 대기 시간이 500 밀리초 (정적 임계값) 보다 큽니다. 
    + 전체 자격 증명 모음 채도가 75% (정적 임계값) 보다 큽니다. 
    + 전체 자격 증명 모음 채도가 평균을 초과 합니다 (동적 임계값).
    + 총 오류 코드는 평균 (동적 임계값) 보다 높습니다. 

   > [!div class="mx-imgBorder"]
   > ![경고에 대해 조건을 선택하는 위치를 나타내는 스크린샷](../media/alert-13.png)

### <a name="example-configure-a-static-alert-threshold-for-latency"></a>예: 대기 시간에 대 한 정적 경고 임계값 구성

1. 신호 이름으로 **전체 서비스 Api 대기 시간** 을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![신호 이름을 선택 하는 것을 보여 주는 스크린샷](../media/alert-14.png)

1. 다음 구성 매개 변수를 사용 합니다.

   + **임계값** 을 **정적** 으로 설정 합니다. 
   + **연산자** 를 **보다 큼** 으로 설정 합니다.
   + **집계 유형** 을 **평균** 으로 설정 합니다.
   + **임계값** 을 **500** 으로 설정 합니다.
   + **집계 세분성 (기간)** 을 **5 분** 으로 설정 합니다.
   + **평가 빈도** 를 **1 분 간격** 으로 설정 합니다.

   > [!div class="mx-imgBorder"]
   > ![정적 경고 임계값의 구성 된 논리를 보여 주는 스크린샷](../media/alert-15.png)

1. **완료** 를 선택합니다.  

### <a name="example-configure-a-dynamic-alert-threshold-for-vault-saturation"></a>예: 자격 증명 모음 채도에 대 한 동적 경고 임계값 구성 

동적 경고를 사용 하는 경우 선택한 key vault의 기록 데이터를 볼 수 있습니다. 파란색 영역은 키 자격 증명 모음의 평균 사용량을 나타냅니다. 빨간색 영역에는 경고 구성의 다른 조건이 충족 되는 경우 경고를 트리거한 스파이크가 표시 됩니다. 빨간색 점은 집계 기간 동안 경고에 대 한 조건이 충족 된 위반 인스턴스를 표시 합니다. 

> [!div class="mx-imgBorder"]
> ![전체 자격 증명 모음 포화도의 그래프를 보여주는 스크린샷.](../media/alert-16.png)

설정된 시간 내에 특정 위반 횟수 후 경고가 발생하도록 설정할 수 있습니다. 과거 데이터를 포함 하지 않으려는 경우 고급 설정에서 제외 하는 옵션이 있습니다. 

1. 다음 구성 매개 변수를 사용 합니다.

   + **임계값** 을 **동적** 으로 설정 합니다. 
   + **연산자** 를 **보다 큼** 으로 설정 합니다.
   + **집계 유형** 을 **평균** 으로 설정 합니다.
   + **임계값 민감도** 를 **중간** 으로 설정 합니다.
   + **집계 세분성 (기간)** 을 **5 분** 으로 설정 합니다.
   + **평가 빈도** 를 **5 분 간격** 으로 설정 합니다.
   + **고급 설정** (선택 사항)을 구성 합니다. 

   > [!div class="mx-imgBorder"]
   > ![동적 경고 임계값의 구성 된 논리를 보여 주는 스크린샷](../media/alert-17.png)

1. **완료** 를 선택합니다.

1. **추가** 를 선택 하 여 구성한 작업 그룹을 추가 합니다.

   > [!div class="mx-imgBorder"]
   > ![작업 그룹을 추가 하는 단추를 보여 주는 스크린샷](../media/alert-18.png)

1. 경고 세부 정보에서 경고를 사용 하도록 설정 하 고 심각도를 할당 합니다.

   > [!div class="mx-imgBorder"]
   > ![경고를 사용하도록 설정하고 심각도를 할당하는 위치를 보여주는 스크린샷.](../media/alert-19.png)

1. 경고를 만듭니다. 

### <a name="example-email-alert"></a>예제 이메일 경고 

위의 단계를 모두 수행 하면 키 자격 증명 모음이 구성 된 경고 조건을 충족 하는 경우 전자 메일 경고를 받게 됩니다. 예를 들어 다음과 같은 전자 메일 경고가 발생 합니다. 

> [!div class="mx-imgBorder"]
> ![이메일 경고를 구성하는 데 필요한 정보를 강조 표시하는 스크린샷.](../media/alert-20.png)

## <a name="next-steps"></a>다음 단계

이 문서에 설정 된 도구를 사용 하 여 주요 자격 증명 모음의 상태를 적극적으로 모니터링 합니다.

- [Key Vault 모니터링](monitor-key-vault.md)
- [Key Vault 데이터 참조 모니터링](monitor-key-vault-reference.md)
