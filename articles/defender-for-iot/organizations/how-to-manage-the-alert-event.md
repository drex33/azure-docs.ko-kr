---
title: 경고 이벤트 관리
description: 네트워크에서 검색된 경고 이벤트를 관리합니다.
ms.date: 05/26/2021
ms.topic: how-to
ms.openlocfilehash: 57d6a2225cc5380d8c1bf4be0955c1b728501cc1
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019815"
---
# <a name="manage-alert-events"></a>경고 이벤트 관리

경고 이벤트를 관리하는 데 사용할 수 있는 옵션은 다음과 같습니다.

 | 작업 | Description |
 |--|--|
 | **Learn** | 감지된 이벤트에 권한을 부여합니다. 자세한 내용은 [학습 및 학습 취소 이벤트 정보](#about-learning-and-unlearning-events)를 참조하세요. |
 | **확인** | 감지된 이벤트에 대해 한 번 경고를 숨깁니다. 이벤트가 다시 감지되면 경고가 다시 트리거됩니다. 자세한 내용은 [확인 및 확인되지 않은 이벤트 정보](#about-acknowledging-and-unacknowledging-events)를 참조하세요. |
 | **음소거** | 동일한 디바이스 및 동급 트래픽이 있는 활동을 계속 무시합니다. 자세한 내용은 [음소거 및 음소거 해제 이벤트 정보](#about-muting-and-unmuting-events)를 참조하세요. |
 
경고 정보를 내보낼 수도 있습니다.
## <a name="about-learning-and-unlearning-events"></a>학습 및 학습 취소 이벤트 정보

학습된 네트워크의 편차를 나타내는 이벤트는 유효한 네트워크 변경 내용을 반영할 수 있습니다. 이러한 변경 내용을 승인하려는 경우 Defender for IoT가 동작을 *학습* 하도록 지시할 수 있습니다. 다음과 같은 예가 있습니다.

- 기존 디바이스에서 새 활동이 감지되었습니다. 예를 들어, 권한 있는 디바이스가 다른 디바이스의 새 리소스에 액세스하려고 시도했습니다.

- 새 디바이스가 네트워크에 추가됩니다.  

- 펌웨어 버전은 표준 유지 관리 절차에 따라 변경됩니다.

- 새 디바이스가 대상 컨트롤러에서 읽기/쓰기 작업을 수행했습니다.

- 새 디바이스는 대상 컨트롤러에서 읽기/쓰기 작업을 수행하며 프로그래밍 디바이스로 정의되어야 합니다.

- 새로운 합법적인 검사가 수행되고 디바이스를 검색 디바이스로 정의해야 합니다.

**이벤트를 알아보려면**: 

1. **경고** 탭으로 이동합니다.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-tab.png" alt-text="화면 왼쪽 탐색 모음에서 경고 탭 선택":::

1. 경고 목록에서 경고를 선택합니다.

1. **Learn** 을 선택합니다.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/learn.png" alt-text="감지된 주소 검사 창입니다.":::

경고에 반영된 활동은 데이터 마이닝, 위험 평가 및 공격 벡터 보고서를 생성할 때 계산됩니다. 해당 이벤트를 관리하면 센서가 적절하게 보고서를 업데이트합니다.

**Learn** 을 선택하면 센서는 트래픽, 구성 또는 활동을 유효한 것으로 간주합니다. 이벤트에 대한 경고가 더 이상 트리거되지 않습니다. 또한 센서가 위험 평가, 공격 벡터, 기타 보고서를 생성할 때 이벤트가 계산되지 않는다는 의미입니다.

학습한 이벤트는 학습을 취소할 수 없습니다. 센서가 이벤트의 학습을 취소할 경우 이 이벤트와 관련된 경고가 다시 트리거됩니다.

**이벤트 학습을 취소하려면**

1. **경고** 탭으로 이동합니다.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-tab.png" alt-text="화면 왼쪽 탐색 모음에서 경고 탭 선택":::

1. 보기 드롭다운 메뉴에서 **승인됨** 을 선택합니다.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/view-acknowledged.png" alt-text="보기 섹션 드롭다운 메뉴에서 승인됨 선택" lightbox="media/how-to-work-with-alerts-sensor/view-acknowledged-expanded.png":::

1. 경고 목록에서 경고를 선택합니다.

1. **학습 취소** 를 선택합니다.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/unlearn.png" alt-text="학습 취소를 선택하여 이벤트 학습 취소":::

이벤트 학습이 취소된 후 기본 보기 페이지로 다시 이동합니다.

**경고가 학습되거나 승인되었는지 확인하려면**:

1. 이벤트 타임라인 탭 :::image type="icon" source="media/how-to-work-with-alerts-sensor/event-timeline.png" border="false":::으로 이동합니다.

1. 타임라인에서 경고를 찾습니다.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/event-timeline-acknowledged.png" alt-text="이벤트 타임라인에서 승인된 이벤트를 찾을 수 있음":::

    경고가 학습되거나 승인된 경우 경고 창에 표시됩니다.
 
## <a name="about-acknowledging-and-unacknowledging-events"></a>확인 및 확인되지 않은 이벤트 정보

특정 상황에서는 센서가 감지된 이벤트를 학습하지 않기를 원하거나 옵션을 사용하지 못할 수 있습니다. 대신 인시던트를 완화해야 할 수도 있습니다. 예를 들면 다음과 같습니다.

- **네트워크 구성 또는 디바이스의 완화**: 네트워크에서 새 디바이스가 감지되었음을 나타내는 경고를 받습니다. 조사할 때 디바이스가 권한이 없는 네트워크 디바이스임을 알 수 있습니다. 네트워크에서 디바이스를 분리하여 인시던트를 처리합니다.
- **센서 구성 업데이트**: 서버에서 과도한 수의 원격 연결을 시작했음을 나타내는 경고를 받습니다. 센서 변칙 임계값이 1분 이내에 세션의 특정 수 이상으로 경고를 트리거하도록 정의되었으므로 이 경고가 트리거되었습니다. 임계값을 업데이트하여 인시던트를 처리합니다.

완화 또는 조사를 수행하고 나면 **확인** 을 선택하여 경고를 숨기도록 센서에 지시할 수 있습니다. 이벤트가 다시 감지되면 경고가 다시 트리거됩니다.

경고를 지우려면 다음을 수행합니다.

  - **확인** 을 선택합니다.

경고를 다시 보려면 다음을 수행합니다.

  - 경고에 액세스하고 **확인 취소** 를 선택합니다.

추가 조사가 필요한 경우 경고의 확인을 취소합니다.

## <a name="about-muting-and-unmuting-events"></a>음소거 및 음소거 해제 이벤트 정보

특정 상황에서는 네트워크의 특정 시나리오를 무시하도록 센서에 지시할 수 있습니다. 예를 들면 다음과 같습니다.

  - **변칙** 엔진이 두 디바이스 간의 대역폭 급증에 대한 경고를 트리거하지만, 해당 디바이스에서 이는 유효한 급증입니다.

  - **프로토콜 위반** 엔진은 두 디바이스 간에 감지된 프로토콜 편차에 대한 경고를 트리거하지만 디바이스 간 편차는 유효합니다.

해당 상황에서는 학습을 사용할 수 없습니다. 학습을 수행할 수 없고 경고를 표시하지 않고 위험과 공격 벡터를 계산할 때 디바이스를 제거하려는 경우 경고 이벤트를 음소거할 수 있습니다.

> [!NOTE] 
> 인터넷 디바이스가 원본 또는 대상으로 정의된 이벤트는 음소거할 수 없습니다.

### <a name="what-alert-activity-is-muted"></a>음소거된 경고 활동은 무엇인가요?

음소거된 시나리오에는 이벤트에 대해 감지된 네트워크 디바이스 및 트래픽이 포함됩니다. 경고 제목은 음소거되는 트래픽을 설명합니다.

음소거되는 디바이스는 경고에 이미지로 표시됩니다. 두 디바이스가 표시되면 둘 사이의 특정 경고 트래픽이 음소거됩니다.

**예 1**

이벤트가 음소거되면 기본 디바이스(원본)가 공급업체에서 정의한 대로 잘못된 함수 코드를 보조 디바이스(대상)에 보낼 때마다 무시됩니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="보조 디바이스가 수신되었습니다.":::

**예 2**

이벤트가 음소거되면 원본에서 대상과 관계없이 잘못된 콘텐츠가 포함된 HTTP 헤더를 보낼 때마다 무시됩니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="잘못된 HTTP 헤더 콘텐츠의 스크린샷.":::

**이벤트가 음소거된 후:**

- 경고는 음소거 해제될 때까지 **확인** 된 경고 보기에서 액세스할 수 있습니다.

- 음소거 동작은 **이벤트 타임라인** 에 표시됩니다.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="이벤트가 감지되고 음소거됩니다.":::

- 센서는 위험 평가, 공격 벡터, 기타 보고서를 생성할 때 디바이스를 다시 계산합니다. 예를 들어 디바이스에서 악성 트래픽을 감지한 경고를 음소거한 경우 해당 디바이스는 위험 평가 보고서에서 계산되지 않습니다.

**경고를 음소거하고 음소거를 해제하려면 다음을 수행합니다.**

- 경고에서 **음소거** 아이콘을 선택합니다.

**음소거됨 경고를 보려면 다음을 수행합니다.**

1. **경고** 주 화면에서 **확인** 됨 옵션을 선택합니다.

2. 경고에 마우스를 올려서 음소거되어 있는지 확인합니다.  

## <a name="export-alert-information"></a>경고 정보 내보내기

경고 정보를 .csv 파일로 내보냅니다. 검색된 모든 경고의 정보를 내보내거나 필터링된 보기를 기반으로 하여 정보를 내보낼 수 있습니다. 다음 정보를 내보냅니다.

- 소스 주소
- 대상 주소
- 경고 제목
- 경고 심각도
- 경고 메시지
- 추가 정보
- 확인된 상태
- PCAP 가용성

내보내려면 다음을 수행합니다.

1. 측면 메뉴에서 경고를 선택합니다.

1. 내보내기를 선택합니다.

1. 여러 디바이스에 적용되는 각 경고에 대해 별도의 행으로 경고 정보를 내보내려면 확장된 경고 내보내기를 선택합니다. 확장된 경고 내보내기를 선택하는 경우 .csv 파일은 각 행의 고유한 항목을 사용하여 경고 이벤트의 중복 행을 만듭니다. 이 옵션을 사용하면 내보낸 경고 이벤트를 더욱 쉽게 조사할 수 있습니다.

## <a name="see-also"></a>참고 항목

[경고 유형 및 설명](alert-engine-messages.md)

[모니터링된 트래픽 제어](how-to-control-what-traffic-is-monitored.md)
