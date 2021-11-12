---
title: Windows 엔드포인트 모니터링 구성
description: WMI(Windows 엔드포인트 모니터링)를 통해 디바이스에서 해결된 데이터를 보강합니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 7b59c0b5dcb14b57ee5e4e12c2dc3e925e30d37d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281838"
---
# <a name="configure-windows-endpoint-monitoring-wmi"></a>WMI(Windows 엔드포인트 모니터링) 구성

Windows 끝점 모니터링 기능을 사용 하 여 IoT 용 Microsoft Defender를 구성 하 여 Windows 시스템을 선택적으로 검색할 수 있습니다. 이렇게 하면 서비스 팩 수준 등 디바이스에 대한 보다 집중적이고 정확한 정보를 얻을 수 있습니다.

특정 범위 및 호스트를 사용하여 검색을 구성하고, 원하는 경우에만 수행되도록 구성할 수 있습니다. Windows 시스템을 관리하는 데 사용되는 Microsoft의 표준 스크립팅 언어인 WMI(Windows Management Instrumentation)를 사용하여 선택적 검색을 수행합니다.

> [!NOTE]
> - 한 번에 하나의 스캔만 실행할 수 있습니다.
> - 도메인 또는 로컬 관리자 권한을 가진 사용자를 사용하면 최상의 결과를 얻을 수 있습니다.
> - WMI 구성을 시작하기 전에 UDP 포트 135 및 1024 위의 모든 TCP 포트를 사용하여 센서에서 스캔된 서브넷으로 나가는 트래픽을 여는 방화벽 규칙을 구성합니다.

프로브가 완료되면 모든 검색 시도를 포함한 로그 파일을 로그 내보내기 옵션에서 사용할 수 있습니다. 로그는 검색된 모든 IP 주소를 포함합니다. 로그는 각 IP 주소마다 성공 및 실패 정보를 표시합니다. 예외에서 파생된 자유 문자열인 오류 코드도 있습니다. 시스템에는 마지막 로그의 스캔만 저장됩니다.

예약된 스캔 또는 수동 스캔을 수행할 수 있습니다. 스캔이 완료되면 CSV 파일로 결과를 볼 수 있습니다.

**필수 구성 요소**

UDP 포트 135 및 1024 이상의 모든 TCP 포트를 사용하여 센서의 나가는 트래픽을 검사된 서브넷으로 여는 방화벽 규칙을 구성합니다.

## <a name="perform-an-automatic-scan"></a>자동 검사 수행

이 섹션에서는 자동 검사를 수행하는 방법을 설명합니다.

**자동 검사를 수행하려면:**

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **Windows 엔드포인트 모니터링**:::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::을 선택합니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Windows 엔드포인트 모니터링 선택을 보여 주는 스크린샷.":::

3. **스캔 일정** 창에서 다음과 같이 옵션을 구성합니다.

      - **고정 간격 기준(시간)** : 시간 간격에 따라 스캔 일정을 설정합니다.

      - **특정 시간 기준**: 특정 시간에 따라 스캔 일정을 설정하고 **스캔 저장** 을 선택합니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="스캔 저장 단추를 보여 주는 스크린샷.":::

4. 스캔 범위를 정의하려면 **스캔 범위 설정** 을 선택합니다.

5. IP 주소 범위를 설정하고 사용자와 암호를 추가합니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="사용자 및 암호 추가를 보여 주는 스크린샷.":::

6. 스캔에서 IP 범위를 제외하려면 범위 옆의 **사용 안 함** 을 선택합니다.

7. 범위를 제거하려면 범위 옆의 :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false":::를 선택합니다.

8. **저장** 을 선택합니다. **검사 범위 구성 편집** 대화 상자가 닫히고 범위 수가 **검사 범위** 창에 표시됩니다.

## <a name="perform-a-manual-scan"></a>수동 검사 수행

**수동 검사를 수행하려면:**

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **Windows 엔드포인트 모니터링**:::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::을 선택합니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Windows 엔드포인트 모니터링 설정 화면을 보여 주는 스크린샷.":::

3. **작업** 창에서 **스캔 시작** 을 선택합니다. **작업** 창에 상태 표시줄이 표시되고 스캔 프로세스의 진행률이 표시됩니다.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="검사 시작 단추를 보여 주는 스크린샷.":::

## <a name="view-scan-results"></a>스캔 결과 보기

**검사 결과를 보려면:**

1. 검사가 완료되면 **작업** 창에서 **검사 결과 보기** 를 선택합니다. 검사 결과가 포함된 CSV 파일이 컴퓨터에 다운로드됩니다.
