---
title: 경고 Azure Key Vault
description: 대시보드를 만들어 키 자격 증명 모음의 상태를 모니터링하고 경고를 구성합니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 4c1f63bdc13822b7eb48dc5410a990dc75f3453e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060785"
---
# <a name="alerting-for-azure-key-vault"></a>Azure Key Vault 대한 경고

## <a name="overview"></a>개요

키 자격 증명 모음을 사용하여 프로덕션 비밀을 저장하기 시작한 후에는 키 자격 증명 모음의 상태를 모니터링하여 서비스가 의도에 맞게 작동하는지 확인하는 것이 중요합니다. 서비스의 크기를 조정하기 시작하면 키 자격 증명 모음으로 전송되는 요청 수가 증가합니다. 이로 인해 요청 대기 시간이 길어질 수 있으며 극단적인 경우에는 서비스의 성능에 영향을 줄 수 있는 요청이 정체됩니다. 액세스 정책 또는 방화벽 구성 문제에 대해 신속히 알 수 있게, 키 자격 증명 모음이 비정상적인 수의 오류 코드를 전송하는 경우에도 경고해야 합니다. 이 문서에서 다루는 토픽은 다음과 같습니다.

+ 모니터링할 기본 Key Vault 메트릭
+ 메트릭을 구성하고 대시보드를 만드는 방법
+ 지정된 임계값에 대한 경고를 만드는 방법

Azure Monitor for Key Vault는 로그와 메트릭을 결합하여 글로벌 모니터링 솔루션을 제공합니다. [Azure Monitor의 Key Vault에 대한 자세한 정보](../../azure-monitor/insights/key-vault-insights-overview.md#introduction-to-key-vault-insights)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Key Vault에 대한 경고를 구성하는 방법 

이 섹션에서는 키 자격 증명 모음이 비정상 상태가 되면 팀에 즉시 조치를 취할 수 있게 알리도록 키 자격 증명 모음에 대한 경고를 구성하는 방법을 보여줍니다. 팀 DL 등에 이메일을 보내거나, 이벤트 그리드 알림을 생성하거나, 전화를 걸거나, 문자 메시지를 보내는 경고를 구성할 수 있습니다. 고정 값을 기준으로 하는 고정 경고나, 모니터링되는 메트릭이 정의된 기간 동안 평균 키 자격 증명 모음 한도를 일정 횟수 초과할 경우 경고하는 동적 경고를 선택할 수도 있습니다. 

> [!IMPORTANT]
> 새로 구성된 경고가 알림을 보내기 시작하려면 최대 10분이 소요될 수 있습니다. 

### <a name="configure-an-action-group"></a>작업 그룹 구성 

작업 그룹은 알림 및 속성의 구성 가능한 목록입니다.

1. Azure Portal에 로그인합니다.
2. 검색 상자에서 **경고** 검색
3. **작업 관리** 선택

> [!div class="mx-imgBorder"]
> ![작업 관리 단추를 강조 표시하는 스크린샷.](../media/alert-6.png)

4. **+ 작업 그룹 추가** 선택

> [!div class="mx-imgBorder"]
> ![+ 작업 그룹 추가 단추를 강조 표시하는 스크린샷.](../media/alert-7.png)

5. 작업 그룹에 대한 **작업 유형** 을 선택합니다. 이 예제에서는 이메일 경고를 만듭니다.

> [!div class="mx-imgBorder"]
> ![작업 그룹을 추가하는 데 필요한 필드를 강조 표시하는 스크린샷.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![이메일 또는 SMS 메시지 경고를 추가하는 데 필요한 항목을 보여주는 스크린샷.](../media/alert-9.png)

6. 페이지 아래쪽에서 **확인** 을 클릭합니다. 작업 그룹을 만들었습니다. 

이제 작업 그룹을 구성했으므로 키 자격 증명 모음 경고 임계값을 구성합니다. 

### <a name="configure-alert-thresholds"></a>경고 임계값 구성 

1. Azure Portal에서 키 자격 증명 모음 리소스를 선택하고 **모니터링** 아래에서 **경고** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![모니터링 섹션에서 경고 메뉴 옵션을 표시하는 스크린샷.](../media/alert-10.png)

2. **새 경고 규칙** 선택

> [!div class="mx-imgBorder"]
> ![+ 새 경고 규칙 단추를 표시하는 스크린샷.](../media/alert-11.png)

3. 경고 규칙의 범위를 선택합니다. 자격 증명 모음을 하나 또는 여러 개 선택할 수 있습니다. 

> [!IMPORTANT]
> 경고 범위에 대해 여러 자격 증명 모음을 선택하는 경우, 선택한 모든 자격 증명 모음이 같은 지역에 있어야 합니다. 다른 지역의 자격 증명 모음에는 별도의 경고 규칙을 구성해야 합니다. 

> [!div class="mx-imgBorder"]
> ![자격 증명 모음을 선택할 수 있는 방법을 표시하는 스크린샷.](../media/alert-12.png)

4. 경고에 대한 조건을 선택합니다. 다음 신호 중 하나를 선택하고 경고에 대한 논리를 정의할 수 있습니다. Key Vault 팀에서 권장하는 경고 임계값 구성은 다음과 같습니다. 

    + Key Vault 가용성이 100% 아래로 떨어짐(고정 임계값)
    + Key Vault 대기 시간이 500ms보다 큼(고정 임계값) 
    + 전체 자격 증명 모음 포화도가 75%보다 큼(고정 임계값) 
    + 전체 자격 증명 모음 포화도가 평균 초과(동적 임계값)
    + 총 오류 코드가 평균보다 높음(동적 임계값) 

> [!div class="mx-imgBorder"]
> ![경고에 대해 조건을 선택하는 위치를 나타내는 스크린샷](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>예제 1: 대기 시간에 대한 고정 경고 임계값 구성

**전체 서비스 API 대기 시간** 을 신호 이름으로 선택
> [!div class="mx-imgBorder"]
> ![전반적인 서비스 API 대기 시간 신호 이름을 표시하는 스크린샷.](../media/alert-14.png)

다음 구성 매개 변수를 참조하세요.

+ 임계값을 **고정** 으로 설정 
+ 연산자를 **초과** 로 설정
+ 집계 유형을 **평균** 으로 설정
+ 임계값을 **500** 으로 설정
+ 집계 기간을 **5분** 으로 설정
+ 평가 빈도를 **1분** 으로 설정
+ **완료** 를 선택합니다.  

> [!div class="mx-imgBorder"]
> ![구성된 경고 논리를 강조 표시하는 스크린샷.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>예제 2: 자격 증명 모음 포화도에 대한 동적 경고 임계값 구성 

동적 경고를 사용할 경우 선택한 키 자격 증명 모음의 과거 데이터를 볼 수 있습니다. 파란색 영역은 키 자격 증명 모음의 평균 사용량을 나타냅니다. 빨간색 영역에는 경고 구성의 다른 기준이 충족되는 경우 경고를 트리거할 스파이크가 표시됩니다. 빨간색 점은 집계 시간 범위 동안 경고 기준이 충족된 위반 인스턴스를 표시합니다. 설정된 시간 내에 특정 위반 횟수 후 경고가 발생하도록 설정할 수 있습니다. 과거 데이터를 포함하지 않으려는 경우 고급 설정 아래에 이전 데이터를 제외하는 옵션이 있습니다. 

> [!div class="mx-imgBorder"]
> ![전체 자격 증명 모음 포화도의 그래프를 보여주는 스크린샷.](../media/alert-16.png)

다음 구성 매개 변수를 참조하세요.

+ 임계값을 **동적** 으로 설정 
+ 연산자를 **초과** 로 설정
+ 집계 유형을 **평균** 으로 설정
+ 임계값 민감도를 **보통** 으로 설정
+ 집계 기간을 **5분** 으로 설정
+ 평가 빈도를 **1분** 으로 설정
+ **선택 사항** 고급 설정 구성 
+ **완료** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷](../media/alert-17.png)

5. 구성한 작업 그룹 추가

> [!div class="mx-imgBorder"]
> ![작업 그룹 추가 방법을 보여주는 스크린샷.](../media/alert-18.png)

6. 경고를 사용하도록 설정하고 심각도 할당

> [!div class="mx-imgBorder"]
> ![경고를 사용하도록 설정하고 심각도를 할당하는 위치를 보여주는 스크린샷.](../media/alert-19.png)

7. 경고 만들기 

### <a name="example-email-alert"></a>예제 이메일 경고 

> [!div class="mx-imgBorder"]
> ![이메일 경고를 구성하는 데 필요한 정보를 강조 표시하는 스크린샷.](../media/alert-20.png)

## <a name="next-steps"></a>다음 단계

축하합니다. 이제 모니터링 대시보드를 만들고 키 자격 증명 모음에 대한 경고를 구성했습니다.

위의 모든 단계를 수행한 후에는, 키 자격 증명 모음이 구성한 경고 기준에 부합하면 이메일 경고를 받게 됩니다. 아래에 예제가 나와 있습니다. 이 문서에서 설정한 도구를 사용하여 키 자격 증명 모음의 상태를 적극적으로 모니터링합니다.

- [Key Vault 모니터링](monitor-key-vault.md)
- [모니터링 Key Vault 데이터 참조](monitor-key-vault-reference.md)