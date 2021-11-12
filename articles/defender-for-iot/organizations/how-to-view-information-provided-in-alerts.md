---
title: 경고 메시지 정보
description: 경고 창에서 경고를 선택하여 세부 정보를 검토합니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: d76b10943932825388075b7aef44f0f5b94c1d53
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278572"
---
# <a name="about-alert-messages"></a>경고 메시지 정보

**경고** 창에서 경고를 선택하여 경고 세부 정보를 검토합니다. 세부 정보에는 다음 정보가 포함됩니다.

- 경고 메타데이터

- 트래픽, 디바이스, 이벤트에 대한 정보

- 디바이스 맵의 연결된 디바이스에 대한 링크

- 보안 분석가 및 관리자가 정의한 설명

- 이벤트 조사에 대한 권장 사항

## <a name="alert-metadata"></a>경고 메타데이터

경고 세부 정보에는 다음과 같은 경고 메타데이터가 포함됩니다.

  - 경고 ID

  - 경고를 트리거한 정책 엔진

  - 경고가 트리거된 날짜 및 시간

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="권한 없는 인터넷 연결이 검색됨":::

## <a name="information-about-devices-traffic-and-the-event"></a>디바이스, 트래픽, 이벤트에 대한 정보

경고 메시지는 다음에 대한 정보를 제공합니다.

  - 검색된 디바이스

  - 디바이스 간에 검색된 트래픽(예: 프로토콜 및 함수 코드)

  - 이벤트의 의미에 대한 인사이트

경고 이벤트를 관리하는 방법을 결정할 때 이 정보를 사용할 수 있습니다.

## <a name="links-to-connected-devices-in-the-device-map"></a>디바이스 맵의 연결된 디바이스에 대한 링크

경고에서 디바이스 이미지를 선택하고 맵에서 연결된 디바이스를 보면 검색된 디바이스에 연결된 디바이스에 대한 자세한 내용을 확인할 수 있습니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="RCP 작업이 실패함":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="디바이스 스크린샷":::

맵은 선택한 디바이스와 해당 디바이스에 연결된 다른 디바이스에 필터를 적용합니다. 맵에는 경고에서 검색된 디바이스에 대한 **빠른 속성** 대화 상자도 표시됩니다.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>보안 분석가 및 관리자가 정의한 설명 

경고에는 미리 정의된 설명 목록이 포함될 수 있습니다. 예를 들면 수행할 완화 작업에 대한 지침 또는 이벤트에 대해 연락할 개인의 이름 등이 설명에 적혀 있을 수 있습니다.

경고 이벤트를 관리할 때 이벤트 상태 또는 경고를 조사하기 위해 수행한 단계를 가장 잘 반영하는 설명을 하나 이상 선택할 수 있습니다.

선택한 설명은 경고 메시지에 저장됩니다. 설명을 사용하면 경고 이벤트를 조사하는 동안 개인과 팀 간의 통신을 개선할 수 있습니다. 결론적으로 설명을 사용하면 인시던트 응답 시간을 단축할 수 있습니다.

관리자 또는 보안 분석가가 설명을 미리 정의합니다. 선택한 설명은 전달 규칙에 정의된 파트너 시스템에 전달되지 않습니다.

경고의 정보를 검토한 후에 경고 이벤트 관리를 안내하는 다양한 포렌식 단계를 수행할 수 있습니다. 예를 들면 다음과 같습니다.

- 최근 디바이스 작업(데이터 마이닝 보고서)을 분석합니다. 

- 동시(이벤트 타임라인)에 발생한 다른 이벤트를 분석합니다. 

- 종합적인 이벤트 트래픽(PCAP 파일)을 분석합니다.

## <a name="pcap-files"></a>PCAP 파일

경우에 따라 경고 메시지에서 PCAP 파일에 액세스할 수 있습니다. 이는 연결된 네트워크 트래픽에 대한 자세한 정보를 원하는 경우에 유용할 수 있습니다.

PCAP 파일을 다운로드하려면 :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="다운로드 아이콘":::을 선택합니다. **경고 세부 정보** 대화 상자의 오른쪽 위에서 선택합니다.

## <a name="recommendations-for-investigating-an-event"></a>이벤트 조사에 대한 권장 사항 

경고의 **권장 사항** 은 이벤트를 보다 잘 이해할 수 있도록 지원하는 정보를 표시합니다. 경고 이벤트를 관리하거나 디바이스 또는 네트워크에서 작업을 수행하기 전에 이 정보를 검토합니다.

## <a name="see-also"></a>참고 항목

[경고 워크플로 가속화](how-to-accelerate-alert-incident-response.md)

[경고 이벤트 관리](how-to-manage-the-alert-event.md)
