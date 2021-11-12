---
title: 경고 워크플로 가속화
description: 경고 및 인시던트 워크플로를 개선합니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: e570cff67630a483d6926f6090165708a5ca17b4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283983"
---
# <a name="accelerate-alert-workflows"></a>경고 워크플로 가속화

이 문서에서는 Microsoft Defender for IoT에서 경고 설명, 경고 그룹 및 사용자 지정 경고 규칙을 사용 하 여 경고 워크플로를 가속화 하는 방법을 설명 합니다.  이러한 도구는 다음과 같은 경우에 유용합니다.

- 네트워크에서 검색된 많은 양의 경고 이벤트를 분석하고 관리합니다.

- 특정 네트워크 활동을 파악하고 처리합니다.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>경고 설명을 사용하여 인시던트 워크플로 가속화

경고 이벤트를 조사하는 동안 개인 및 팀 간의 통신을 향상시키려면 경고 설명을 사용합니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of-malicious-activity-screen.png" alt-text="악의적 활동을 보여 주는 스크린샷":::

경고 설명을 사용하여 다음을 개선합니다.

- **워크플로 단계**: 경고 완화 단계를 제공합니다.

- **워크플로 후속 작업**: 단계가 수행되었음을 알립니다.

- **워크플로 지침**: 이벤트에 대한 권장 사항, 인사이트 또는 경고를 제공합니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="경고 설명을 표시하는 스크린샷":::

사용 가능한 옵션 목록이 각 경고에 표시됩니다. 사용자는 하나 또는 여러 개의 메시지를 선택할 수 있습니다.

경고 설명을 추가하려면

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **시스템 설정** 창에서 **경고 설명** 을 선택합니다.

3. **설명 추가** 상자에 설명 텍스트를 입력합니다. 최대 50자를 사용합니다. 쉼표는 허용되지 않습니다.

4. **추가** 를 선택합니다.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>경고 그룹을 사용하여 인시던트 워크플로 가속화

경고 그룹을 통해 SOC 팀은 SIEM 솔루션에서 경고를 보고 필터링한 다음, 엔터프라이즈 보안 정책 및 비즈니스 우선 순위에 따라 이러한 경고를 관리할 수 있습니다. 예를 들어 새 검색에 대한 경고는 검색 그룹으로 구성됩니다. 이 그룹에는 새 디바이스, 새 VLAN, 새 사용자 계정, 새 MAC 주소 등의 검색을 처리하는 경고가 포함됩니다.

경고 그룹은 다음과 같은 파트너 솔루션에 대한 전달 규칙을 만들 때 적용됩니다.

  - Syslog 서버

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="전달 규칙을 만드는 방법을 보여 주는 스크린샷":::

관련 경고 그룹이 파트너 출력 솔루션에 표시됩니다. 

### <a name="requirements"></a>요구 사항

다음 접두사가 포함된 지원되는 파트너 솔루션에 경고 그룹이 표시됩니다.

- **cat**: QRadar, ArcSight, Syslog CEF, Syslog LEEF 관련

- **경고 그룹**: Syslog 텍스트 메시지 관련

- **alert_group**: Syslog 개체 관련

이러한 필드는 파트너 솔루션에서 경고 그룹 이름을 표시하도록 구성해야 합니다. 경고 그룹과 연결된 경고가 없으면 파트너 솔루션의 필드에 **NA** 가 표시됩니다.

### <a name="default-alert-groups"></a>기본 경고 그룹

자동으로 정의되는 경고 그룹은 다음과 같습니다.

- 비정상적인 통신 동작
- 사용자 지정 경고
- 원격 액세스
- 비정상적인 HTTP 통신 동작
- 검색
- 다시 시작 및 중지 명령
- 인증
- 펌웨어 변경
- 검사
- 무단 통신 동작
- 잘못된 명령
- 센서 트래픽
- 대역폭 이상
- 인터넷 액세스
- 맬웨어가 의심됨
- 버퍼 오버플로 
- 작업 실패
- 악의적 활동이 의심됨
- 명령 실패
- 운영 문제
- 구성 변경 내용
- 프로그래밍

경고 그룹은 미리 정의되어 있습니다. 경고 그룹과 연결된 경고에 대한 자세한 내용 및 사용자 지정 경고 그룹을 만드는 방법에 대한 자세한 내용은 [Microsoft 지원](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099)을 참조하세요.

## <a name="customize-alert-rules"></a>경고 규칙 사용자 지정

사용자 지정 경고 규칙을 사용하여 관심 있는 활동을 보다 구체적으로 파악합니다.

다음을 기준으로 사용자 지정 경고 규칙을 추가할 수 있습니다.

- 범주(예: 프로토콜, 포트 또는 파일)
- 원본 및 대상 주소
- 선택한 범주를 기준으로 하는 조건(예: 프로토콜, 파일 이름, 포트 또는 전송 번호에 연결된 함수)입니다.
- 날짜 및 시간 참조를 기준으로 하는 조건(예: 검색이 특정 날 또는 해당 날의 특정 부분에 수행되었는지)

센서가 규칙에 설명된 활동을 감지하면 경고가 전송됩니다. 개별 센서가 검색하는 정보. 예를 들어, 센서에 원본 IP, 대상 IP 또는 명령(프로토콜 내)을 기준으로 경고를 트리거하도록 지시하는 규칙을 정의합니다. 센서가 규칙에 정의된 트래픽을 감지하면 경고나 이벤트가 생성됩니다.

또한 경고 규칙 작업을 사용하여 Defender for IoT에 다음을 수행하도록 지시할 수 있습니다.

- 사용자가 경고에서 PCAP 파일에 액세스할 수 있도록 허용합니다.
- 경고 심각도를 할당합니다.
- 경고가 아닌 이벤트를 생성합니다. 검색된 정보가 이벤트 타임라인에 표시됩니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="사용자 정의 규칙을 보여 주는 스크린샷":::

경고 메시지는 사용자 정의 규칙이 경고를 트리거했음을 나타냅니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="사용자 지정된 경고를 보여 주는 스크린샷":::

**사용자 지정 경고 규칙을 만들려면**

1. 센서의 측면 메뉴에서 **사용자 지정 경고** 를 선택합니다.
1. 더하기 기호( **+** )를 선택하여 규칙을 만듭니다.
1. 규칙 이름을 정의합니다.
1. **범주** 창에서 범주 또는 프로토콜을 선택합니다.
1. 특정 원본 및 대상 IP 또는 MAC 주소를 정의하거나 임의의 주소를 선택합니다.
1. 하나 또는 여러 규칙 조건을 정의합니다. 다음 두 가지 범주의 조건을 만들 수 있습니다.
    - 선택한 범주와 연결된 고유 값을 기준으로 하는 조건. 추가를 선택하고 값을 정의합니다.
    - 활동이 검색된 시간을 기준으로 하는 조건. 검색 섹션에서 경고를 보내기 위해 검색을 수행해야 하는 시간 및 요일을 선택합니다. 작업이 항상 검색되거나, 작업 시간 도중 또는 이후에 검색될 경우 경고를 보내도록 선택할 수 있습니다. 작업 시간 정의 옵션을 사용하여 조직에 대한 작업 시간을 Defender for IoT에 알릴 수 있습니다.
1. 규칙 작업 정의: 
    - 규칙이 **경보** 또는 **이벤트** 를 트리거하는지를 나타냅니다.
    - 경고에 심각도 수준을 할당합니다.
    - 경고에 PCAP 파일이 포함되어 있는지 여부를 나타냅니다.
1. **저장** 을 선택합니다.

규칙은 **사용자 지정된 경고 규칙** 목록에 추가됩니다. 여기서 기본 규칙 매개 변수를 검토하고, 규칙이 마지막으로 트리거된 시간 등을 확인할 수 있습니다. 목록에서 규칙을 사용하거나 사용하지 않도록 설정할 수도 있습니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="사용자가 추가한 사용자 지정 규칙의 스크린샷":::

### <a name="see-also"></a>참고 항목

[경고에 제공된 정보 보기](how-to-view-information-provided-in-alerts.md)

[경고 이벤트 관리](how-to-manage-the-alert-event.md)
