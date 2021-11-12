---
title: 독점 프로토콜 관리(Horizon)
description: Defender for IoT Horizon은 독점 프로토콜을 실행하는 IoT 및 ICS 디바이스를 보호하는 데 사용되는 ODE(개방형 개발 환경)를 제공합니다.
ms.date: 11/09/2021
ms.topic: reference
ms.openlocfilehash: 6a9995ec70f061381f1315e03c3781537733d97f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347288"
---
# <a name="defender-for-iot-horizon"></a>Defender for IoT Horizon

Defender for IoT Horizon에는 독점 프로토콜을 실행하는 IoT 및 ICS 디바이스를 보호하는 데 사용되는 ODE(개방형 개발 환경)가 있습니다.

Horizon은 다음을 제공합니다.

  - 공용, 독점, 사용자 지정 프로토콜 또는 모든 표준에서 벗어난 프로토콜에 대한 무제한적이고 완벽한 지원. 
  - 새로운 차원의 DPI 개발 유연성 및 범위.
  - 새 버전으로 업그레이드할 필요 없이 OT 가시성 및 제어 능력을 대폭 확장하는 도구.
  - 중요한 정보 유출 없이 독점 개발할 수 있도록 지원하는 보안 기능.

Horizon SDK를 사용하면 자동화된 Defender for IoT 네트워크 분석 프로그램에서 처리할 수 있도록 네트워크 트래픽을 디코딩하는 디섹터 플러그인을 디자인할 수 있습니다.

프로토콜 디섹터는 외부 플러그인으로 개발되며, 모니터링, 경고, 보고 기능을 제공하는 서비스 등의 광범위한 Defender for IoT 서비스에 통합됩니다.

ODE(Open Development Environment) SDK 작업 및 프로토콜 플러그 인 만들기에 대한 자세한 내용은 <ms-horizon-support@microsoft.com>에 문의하세요.

플러그인이 개발되면 Horizon 웹 콘솔을 사용하여 다음을 수행할 수 있습니다.

  - 플러그인 업로드
  - 플러그인 사용 및 사용하지 않음  
  - 플러그인 모니터링 및 디버그를 통한 성능 평가
  - 독점 프로토콜을 기반으로 하는 사용자 지정 경고 만들기 콘솔에 이를 표시하고 파트너 공급업체에 전달 

    :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-plugin.png" alt-text="Horizon 플러그인을 통해 업로드합니다."::: 

이 기능은 관리자, Cyberx 또는 Support 사용자가 사용할 수 있습니다.

Horizon 콘솔에 로그인하려면 다음을 수행합니다.

1. CLI를 통해 센서에 로그인합니다.
2. `/var/cyberx/properties/horizon.properties` 파일에서 `ui.enabled` 속성을 `true`로 변경합니다(`horizon.properties:ui.enabled=true`).
3. 센서 콘솔에 로그인합니다. 
4. 주 메뉴에서 **Horizon** 을 선택합니다. 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-from-the-menu.png" alt-text="주 메뉴에서 Horizon을 선택합니다.":::  

Horizon 콘솔에 Defender for IoT에서 제공하는 인프라 플러그인 및 직접 만들고 업로드한 다른 플러그인이 표시됩니다. 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/infrastructure.png" alt-text="Horizon 인프라의 스크린샷.":::

## <a name="upload-plugins"></a>플러그인 업로드

독점 디섹터 플러그인을 만들고 테스트한 후에는 Horizon 콘솔에서 이를 업로드하고 모니터링할 수 있습니다.

업로드하려면 다음을 수행합니다.

1. 콘솔에서 **업로드** 를 선택합니다.

   :::image type="content" source="media/how-to-manage-proprietary-protocols/upload-a-plugin.png" alt-text="업로드할 플러그인을 선택합니다.":::

2. 플러그인을 끌거나 플러그인으로 이동합니다. 업로드가 실패하면 오류 메시지가 표시됩니다.

ODE(Open Development Environment) SDK 작업 및 프로토콜 플러그 인 만들기에 대한 자세한 내용은 <ms-horizon-support@microsoft.com>에 문의하세요.

## <a name="enable-and-disable-plugins"></a>플러그인 사용 및 사용하지 않음

토글 단추를 사용하여 플러그인을 사용 및 사용하지 않습니다. 비활성화하면 트래픽이 더 이상 모니터링되지 않습니다.

인프라 플러그인은 비활성화할 수 없습니다.

## <a name="monitor-plugin-performance"></a>플러그인 성능 모니터링 

Horizon 콘솔 개요 창에서는 업로드한 플러그인에 대한 기본 정보를 제공하며, 여기서 플러그인을 사용하지 않거나 사용할 수 있습니다.

:::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-overview.png" alt-text="Horizon 개요 페이지의 스크린샷."::: 

| 애플리케이션 | 업로드한 플러그인의 이름입니다. |
|--|--|
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/toggle-icon.png" border="false"::: | 플러그인을 켜거나 끕니다. 플러그인을 끄면 센서는 플러그인에 정의된 프로토콜 트래픽을 처리하지 않습니다. |
| 시간 | 데이터를 마지막으로 분석한 시간입니다. 5초마다 업데이트됩니다. |
| PPS | 초당 패킷 수입니다. |
| 대역폭 | 최근 5초 이내에 감지된 평균 대역폭입니다. |
| 잘못된 형식 | 프로토콜이 유효성 검사를 통해 문제가 없다고 판명된 이후에는 잘못된 형식에 대한 유효성 검사가 사용됩니다. 프로토콜에 기반한 패킷을 처리하는 데 실패하면 실패 응답이 반환됩니다.<br/> <br />이 열은 지난 5초간 발생한 잘못된 형식 오류의 개수를 나타냅니다. |
| 경고 | 플러그인 경고 구성에 따르면, 패킷이 구조 및 사양에 부합하지만, 예기치 않은 동작이 있습니다. |
| 오류 | 패킷이 프로토콜 정의에 부합하는지 판정하는 기본 프로토콜 유효성 검사에 실패한 패킷 수입니다.  여기에 표시된 숫자는 지난 5초간 감지된 오류 수를 나타냅니다. |
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/monitor-icon.png" border="false"::: | 플러그인에서 감지된 잘못된 형식 및 경고에 대한 세부 정보를 검토합니다. |

### <a name="plugin-performance-details"></a>플러그인 성능 세부 정보

플러그인에서 감지된 잘못된 형식 및 경고 개수를 분석하여 실시간 플러그인 성능을 모니터링할 수 있습니다. 추가 조사를 위해 화면을 고정하고 내보낼 수 있습니다.

:::image type="content" source="media/how-to-manage-proprietary-protocols/snmp-monitor.png" alt-text="SNMP 모니터링 개요 스크린샷.":::

### <a name="horizon-logs"></a>Horizon 로그

Horizon 분석 정보는 분석 세부 정보, 분석 로그, 내보내기 로그에서 내보내기를 통해 확인할 수 있습니다.

:::image type="content" source="media/how-to-manage-proprietary-protocols/export-logs-details.png" alt-text="내보내기 로그의 분석 세부 정보.":::

## <a name="trigger-horizon-alerts"></a>Horizon 경고 트리거 

Horizon 프레임워크 트래픽 디섹터를 기반으로 하는 모든 프로토콜에 대한 사용자 지정 경고를 트리거하여 기업의 경고 관리 수준을 높입니다. 

다음 경고는 정보를 전달하는 데 사용할 수 있습니다.  

  - 독점 Horizon 플러그인에서 프로토콜 및 기본 프로토콜을 기반으로 하는 트래픽 감지 정보.

  - 모든 프로토콜 계층의 프로토콜 필드 조합 정보. 예를 들어 MODBUS를 실행하는 환경에서 센서가 특정 IP 주소 및 이더넷 대상의 메모리 레지스터에 대한 쓰기 명령을 감지하거나 특정 IP 주소에 대한 액세스를 수행하는 경우 경고를 생성할 수 있습니다.

경고는 Horizon 경고 및 규칙 조건이 충족될 때 트리거됩니다.

  :::image type="content" source="media/how-to-manage-proprietary-protocols/custom-alert-rules.png" alt-text="Horizon에 대한 샘플 사용자 지정 규칙입니다.":::

또한 사용자 지정 경고를 사용하여 고유의 경고 제목과 메시지를 작성할 수 있습니다. 확인된 프로토콜 필드와 값을 경고 메시지 텍스트에 포함할 수도 있습니다.

사용자 지정된 조건부 경고 트리거 및 메시지는 특정 네트워크 작업을 정확하게 파악하고 보안, IT, 운영 팀에 새로운 정보를 효과적으로 업데이트하는 데 도움이 됩니다.

### <a name="working-with-horizon-alerts"></a>Horizon 경고 사용

Horizon 사용자 지정 경고 규칙에 의해 생성된 경고는 전달 규칙을 사용할 때 센서 및 관리 콘솔 경고 창과 통합 파트너 시스템에 표시됩니다. 

Horizon에서 생성된 경고는 승인 또는 음소거 처리할 수 있습니다. 정책 기준으로는 경고 이벤트를 파악할 수 없으므로 사용자 지정 경고에 학습 옵션을 사용할 수 없습니다.

전달 규칙을 사용하는 경우 경고 정보가 파트너 공급업체에 전달됩니다.

Horizon 사용자 지정 경고에서는 심각도가 중요합니다.

사용자 지정 경고에는 **이 이벤트 관리** 섹션 아래의 정적 텍스트가 포함되며 이 텍스트는 조직의 보안 팀에서 경고를 생성했음을 나타냅니다.

### <a name="required-permissions"></a>필요한 사용 권한

Defender for IoT 사용자로 정의된 사용자에게는 사용자 지정 경고 규칙을 만들 수 있는 권한이 있습니다.

### <a name="about-creating-rule-conditions"></a>규칙 조건 만들기 정보

규칙 조건은 경고를 트리거하기 위해 감지해야 하는 네트워크 트래픽을 설명합니다. 규칙 조건은 하나 또는 여러 개의 필드, 연산자 및 값 집합으로 구성될 수 있습니다. **AND** 를 사용하여 조건 집합을 만듭니다.

규칙 조건 또는 조건 집합이 충족되면 경고가 전송됩니다. 조건 논리가 유효하지 않은 경우 알림이 표시됩니다.

  :::image type="content" source="media/how-to-manage-proprietary-protocols/and-condition.png" alt-text="사용자 지정 규칙에 AND 조건을 사용합니다.":::

한 프로토콜에 대해 여러 규칙을 만들 수도 있습니다. 즉, 규칙 조건이 충족되면 만든 규칙별로 경고가 트리거됩니다.

### <a name="about-titles-and-messages"></a>제목 및 메시지 정보

경고 메시지에는 입력한 영숫자 문자와 감지된 트래픽 변수가 포함될 수 있습니다. 예를 들어 경고 메시지에 감지된 원본 및 대상 주소가 포함됩니다. 다양한 언어가 지원됩니다.

### <a name="about-alert-recommendations"></a>경고 권장 사항 정보 

사용자 지정 경고에는 **이 이벤트 관리** 섹션 아래의 정적 텍스트가 포함되며 이 텍스트는 조직의 보안 팀에서 경고를 생성했음을 나타냅니다. 경고 주석을 사용하여 경고를 읽는 개인 및 팀 간의 통신을 개선할 수도 있습니다. 

## <a name="create-horizon-alert-rules"></a>Horizon 경고 규칙 만들기

이 문서에서는 경고 규칙을 만드는 방법을 설명합니다.

Horizon 사용자 지정 경고를 만들려면:

1. Horizon 콘솔의 플러그인 메뉴에서 플러그인을 마우스 오른쪽 단추로 클릭합니다.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/plugins-menu.png" alt-text="메뉴에서 플러그인을 마우스 오른쪽 단추로 클릭합니다.":::

2. **Horizon 사용자 지정 경고** 를 선택합니다. 선택한 플러그인에 대한 **규칙** 창이 열립니다.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/sample-rule-window.png" alt-text="플러그인에 대한 샘플 규칙 창이 열립니다.":::

3. 제목 필드에 제목을 입력합니다.

4. 메시지 필드에 경고 메시지를 입력합니다. 감지된 필드 매개 변수를 메시지에 포함하려면 중괄호(`{}`)를 사용합니다. 첫 번째 중괄호를 입력하면 관련 필드가 표시됩니다.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/rule-window.png" alt-text="감지된 필드를 포함하려면 규칙 창에서 {}를 사용합니다.":::

5. 경고 조건을 정의합니다.

   :::image type="content" source="media/how-to-manage-proprietary-protocols/define-conditions.png" alt-text="경고의 조건을 정의합니다.":::

6. **변수** 를 선택합니다. 변수는 플러그인에서 구성된 필드를 나타냅니다.

7. **연산자** 를 선택합니다.

    - 같음
    
    - 같지 않음
    
    - 보다 작음
    
    - 작거나 같음
    
    - 초과
    
    - 크거나 같음

8. 숫자로 **값** 을 입력합니다. 선택한 변수가 MAC 주소 또는 IP 주소인 경우 값은 점이 포함된 10진수 주소 형식에서 10진수 형식으로 변환해야 합니다. IP 주소 변환 도구(예: <https://www.ipaddressguide.com/ip>)를 사용합니다.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/ip-address-value.png" alt-text="변환된 IP 주소 값.":::

9. 조건 집합을 만들려면 **AND** 를 선택합니다.

10. **SAVE**(저장)를 선택합니다. 규칙이 규칙 섹션에 추가됩니다.

### <a name="edit-and-delete-horizon-custom-alert-rules"></a>사용자 지정 경고 규칙 편집 및 삭제

필요에 따라 편집 및 삭제 옵션을 사용합니다. 특정 규칙은 내장되어 편집하거나 삭제할 수 없습니다.

### <a name="create-multiple-rules"></a>여러 규칙 만들기

여러 규칙을 만드는 경우 모든 규칙 조건 또는 조건 집합이 유효할 때 경고가 트리거됩니다.

## <a name="see-also"></a>참고 항목

[경고에 제공된 정보 보기](how-to-view-information-provided-in-alerts.md)
