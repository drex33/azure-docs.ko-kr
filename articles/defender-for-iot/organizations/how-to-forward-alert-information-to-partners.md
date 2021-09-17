---
title: 경고 정보 전달
description: 전달 규칙을 사용하여 경고 정보를 파트너 시스템에 보낼 수 있습니다.
ms.date: 08/29/2021
ms.topic: how-to
ms.openlocfilehash: 2136a58a383bb623edca69cb03c1c9c5530a107f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432364"
---
# <a name="forward-alert-information"></a>경고 정보 전달

IoT용 Azure Defender를 syslog 서버, 전자 메일 주소 등에 통합하는 파트너에게 경고 정보를 보낼 수 있습니다. 전달 규칙을 사용하면 경고 정보를 보안 관련자에게 신속하게 제공할 수 있습니다.

전달 규칙을 트리거할 조건을 정의합니다. 전달 규칙 조건을 사용하면 센서에서 외부 시스템으로 전송되는 정보의 양을 정확하게 파악하고 관리할 수 있습니다.

Syslog 및 기타 기본 전달 동작은 시스템과 함께 제공됩니다. Microsoft Azure Sentinel, ServiceNow 또는 Splunk와 같은 파트너 공급 업체와 통합하는 경우 더 많은 전달 동작을 사용할 수도 있습니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="경고 정보.":::

Defender for IoT 관리자는 전달 규칙을 사용할 수 있는 권한이 있습니다.

## <a name="about-forwarded-alert-information"></a>전달된 경고 정보에 대해

경고는 보안 및 운영 이벤트의 광범위한 정보를 제공합니다. 예를 들면 다음과 같습니다.

- 경고의 날짜 및 시간

- 이벤트를 검색한 엔진

- 경고 제목 및 설명 메시지

- 경고 심각도

- 원본 및 대상 이름과 IP 주소

- 의심스러운 트래픽이 탐지됨

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="주소 검색이 감지됨.":::

관련 정보는 전달 규칙이 생성될 때 파트너 시스템으로 전송됩니다.

## <a name="about-forwarding-rules-and-certificates"></a>전달 규칙 및 인증서 정보

특정 전달 규칙은 센서 또는 온-프레미스 관리 콘솔과 통합 공급 업체의 서버 간의 암호화 및 인증서 유효성 검사를 허용 합니다.

이러한 경우 센서 또는 온-프레미스 관리 콘솔은 세션의 클라이언트 및 개시자입니다.  인증서는 일반적으로 서버에서 수신 되거나, 통합을 설정 하기 위해 특정 인증서가 제공 되는 비대칭 암호화를 사용 합니다.

IoT 용 Defender 시스템은 인증서의 유효성을 검사 하거나 인증서 유효성 검사를 무시 하도록 설정 되었습니다.  유효성 검사 설정 및 해제에 대 한 자세한 내용은 [인증서 유효성 검사](how-to-deploy-certificates.md#about-certificate-validation) 정보를 참조 하세요.

유효성 검사를 사용 하도록 설정 하 고 인증서를 확인할 수 없으면 Defender IoT와 서버 간의 통신이 중단 됩니다.  센서는 유효성 검사 오류를 나타내는 오류 메시지를 표시 합니다.  유효성 검사를 사용 하지 않도록 설정 하 고 인증서가 유효 하지 않은 경우에도 통신이 수행 됩니다.

다음 전달 규칙은 암호화 및 인증서 유효성 검사를 허용 합니다.
- Syslog CEF
- Azure Sentinel
- QRadar

## <a name="create-forwarding-rules"></a>전달 규칙 만들기

**센서에서 새 전달 규칙을 만들려면 다음을 수행합니다.**

1. 센서에 로그인합니다.

1. 측면 메뉴에서 **전달** 을 선택합니다.

1. **전달 규칙 만들기** 를 선택합니다.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule-screen.png" alt-text="전달 규칙 만들기 아이콘":::

1. 전달 규칙의 이름을 입력합니다.

1. 심각도 수준을 선택합니다.

   심각도 수준을 기준으로, 전달할 최소 인시던트입니다. 예를 들어 **경미** 를 선택하면 경미한 경고 및 이 심각도 수준 이상의 모든 경고가 전달됩니다. 수준은 미리 정의됩니다.

1. 적용할 프로토콜을 선택합니다.

   검색된 트래픽이 특정 프로토콜을 통해 실행된 경우에만 전달 규칙을 트리거합니다. 드롭다운 목록에서 필요한 프로토콜을 선택하거나 모두 선택합니다.

1. 규칙을 적용할 엔진을 선택합니다.

   필요한 엔진을 선택하거나 모두 선택합니다. 선택한 엔진의 경고가 전송됩니다. 

1. 적용할 작업을 선택하고 선택한 작업에 필요한 매개 변수를 입력합니다.

   전달 규칙 작업은 센서에게 파트너 공급 업체 또는 서버에 경고 정보를 전달하도록 지시합니다. 각 전달 규칙에 대해 여러 작업을 만들 수 있습니다.

1. 원하는 경우 다른 작업을 추가합니다.

1. **제출** 을 선택합니다.

### <a name="email-address-action"></a>전자 메일 주소 작업

경고 정보를 포함하는 메일을 전송합니다. 규칙에 따라 하나의 전자 메일 주소를 입력할 수 있습니다.

전달 규칙에 대한 전자 메일을 정의하려면:

1. 단일 전자 메일 주소를 입력합니다. 메일을 두 개 이상 추가해야 하는 경우 각 메일 주소에 대해 다른 작업을 만들어야 합니다.

1. SIEM에서 경고 검색에 대한 타임 스탬프에 대한 표준 시간대를 입력합니다.

1. **제출** 을 선택합니다.

### <a name="syslog-server-actions"></a>Syslog 서버 작업

다음 형식이 지원됩니다.

- 문자 메시지

- CEF 메시지

- LEEF 메시지

- 개체 메시지

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="전달 규칙 작업 생성.":::

다음 매개 변수를 입력합니다.

- Syslog 호스트 이름 및 포트입니다.

- 프로토콜 TCP와 UDP.

- SIEM에서 경고 검색에 대한 타임 스탬프에 대한 표준 시간대.

- CEF 서버에 대한 TLS 암호화 인증서 파일 및 키 파일 (옵션).

:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="전달 규칙에 대한 암호화를 구성합니다.":::

| Syslog 텍스트 메시지 출력 필드 | Description |
|--|--|
| 날짜 및 시간 | Syslog 서버 시스템이 정보를 수신한 날짜 및 시간입니다. |
| 우선 순위 | 사용자.경고 |
| 호스트 이름 | Sensor IP 주소 |
| 프로토콜 | TCP 또는 UDP |
| 메시지 | 센서: 센서 이름.<br /> 알림: 경고의 제목입니다.<br /> 유형: 경고의 형식입니다. **프로토콜 위반**, **정책 위반**, **맬웨어**, **비정상** 또는 **운영상** 이 될 수 있습니다.<br /> 심각도: 경고의 심각도. **경고**, **부분**, **다수** 또는 **중요** 일 수 있습니다.<br /> 원본: 디바이스 이름을 확인합니다.<br /> 원본 디바이스 IP 주소<br /> Destination: 대상 디바이스 이름입니다.<br /> 대상 IP: 대상 디바이스의 IP 주소입니다.<br /> msg: 경고의 메시지.<br /> 경고 그룹: 경고와 연결된 경고 그룹입니다. |

| Syslog 개체 출력 | Description |
|--|--|
| 날짜 및 시간 | Syslog 서버 시스템이 정보를 수신한 날짜 및 시간입니다. |  
| 우선 순위 | 사용자.경고 |
| 호스트 이름 | 센서 IP |
| 메시지 | 센서 이름: 어플라이언스 이름. <br /> 경고 시간: 경고가 탐지된 시간: syslog 서버 컴퓨터의 시간과 다를 수 있으며 전달 규칙의 표준 시간대 구성에 따라 달라집니다. <br /> 경고 제목: 경고의 제목. <br /> 경고 메세지: 경고의 메시지. <br /> 경고 심각도: 경고의 심각한 수준: **경고**, **경미**, **중대** 또는 **위험**. <br /> 경고 형식: **프로토콜 위반**, **정책 위반**, **맬웨어**, **비정상** 또는 **운영상**. <br /> 프로토콜: 경고의 프로토콜.  <br /> **Source_MAC**: IP 주소, 이름, 공급 업체 또는 원본 디바이스의 OS. <br /> Destination_MAC: IP 주소, 이름, 공급 업체 또는 대상 디바이스의 OS. 데이터가 누락된 경우, 값은 **N/A** 가 됩니다. <br /> 경고 그룹: 경고와 연결된 경고 그룹입니다. |

| Syslog CEF 출력 형식 | Description |
|--|--|
| 날짜 및 시간 | Syslog 서버 시스템이 정보를 수신한 날짜 및 시간입니다. |
| 우선 순위 | 사용자.경고 |
| 호스트 이름 | Sensor IP 주소 |
| 메시지 | CEF:0 <br />IoT용 Azure Defender <br />센서 이름: 센서 어플라이언스의 이름. <br />센서 버전 <br />경고 제목: 경고의 제목. <br />msg: 경고의 메시지. <br />프로토콜 - 경고의 프로토콜. <br />심각도: **경고**, **경미**, **중대** 또는 **위험**. <br />형식:  **프로토콜 위반**, **정책 위반**, **맬웨어**, **비정상** 또는 **운영상**. <br /> 시작: 경고가 검색된 시간. <br />syslog 서버 컴퓨터의 시간과 다를 수 있으며 전달 규칙의 시간대 구성에 따라 달라집니다. <br />src_ip: 원본 디바이스의 IP 주소.  <br />dst_ip: 대상 디바이스의 IP 주소.<br />cat: 경고와 연결된 경고 그룹.  |

| Syslog LEEF 출력 형식 | Description |
|--|--|
| 날짜 및 시간 | Syslog 서버 시스템이 정보를 수신한 날짜 및 시간입니다. |  
| 우선 순위 | 사용자.경고 |
| 호스트 이름 | 센서 IP |
| 메시지 | 센서 이름: IoT 어플라이언스 용 Azure Defender의 이름. <br />LEEF:1.0 <br />IoT용 Azure Defender <br />센서  <br />센서 버전 <br />IoT 경고용 Azure Defender <br />제목: 경고의 제목. <br />msg: 경고의 메시지. <br />프로토콜: 경고의 프로토콜.<br />심각도: **경고**, **경미**, **중대** 또는 **위험**. <br />형식: 경고의 형식: **프로토콜 위반**, **정책 위반**, **맬웨어**, **비정상** 또는 **운영상**. <br />시작: 경고의 시간.syslog 서버 머신의 시간과 다를 수도 있습니다. (이는 표준 시간대 구성에 따라 달라집니다.) <br />src_ip: 원본 디바이스의 IP 주소.<br />dst_ip: 대상 디바이스의 IP 주소. <br />cat: 경고와 연결된 경고 그룹. |

모든 정보를 입력한 후 **제출** 을 선택합니다.

### <a name="webhook-server-action"></a>웹후크 서버 작업

웹후크 서버에 경고 정보를 보냅니다. 웹후크 서버를 사용하면 Defender for IoT를 사용하여 경고 이벤트를 구독하는 통합을 설정할 수 있습니다. 경고 이벤트가 트리거되면 관리 콘솔은 웹후크의 구성된 URL에 HTTP POST 페이로드를 보냅니다. 웹후크는 외부 SIEM 시스템, SOAR 시스템, 인시던트 관리 시스템 등을 업데이트하는 데 사용할 수 있습니다.

**웹후크 작업을 정의하려면:**

1. 웹후크 작업을 선택합니다.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/webhook.png" alt-text="웹후크 전달 규칙을 정의합니다.":::

1. **URL** 필드에 서버 주소를 입력합니다.

1. **키** 및 **값** 필드에서 키와 값 정의를 사용하여 HTTP 헤더를 사용자 지정합니다. 키는 문자, 숫자, 대시 및 밑줄만 포함할 수 있습니다. 값에는 선행 및/또는 후행 공백이 하나만 포함될 수 있습니다.

1. **저장** 을 선택합니다.

### <a name="webhook-extended"></a>Webhook 확장

Webhook 확장은 끝점에 추가 데이터를 보내는 데 사용할 수 있습니다. 확장 기능은 웹 후크 경고의 모든 정보를 포함 하 고 보고서에 다음 정보를 추가 합니다.

- sensorID
- sensorName
- zoneID
- zoneName
- siteID
- siteName
- sourceDeviceAddress
- destinationDeviceAddress
- remediationSteps
- handled
- additionalInformation

**Webhook 확장 작업을 정의 하려면**:

1. 관리 콘솔의 왼쪽 창에서 **전달** 을 선택 합니다.

1. 단추를 선택하여 전달 규칙을 :::image type="icon" source="media/how-to-forward-alert-information-to-partners/add-icon.png" border="false"::: 추가합니다.

1. 전달 경고에 대한 의미 있는 이름을 추가합니다.

1. 심각도 수준을 선택합니다.

1. **추가** 를 선택합니다.

1. 형식 선택 드롭다운 창에서 **Webhook 확장** 를 선택합니다.

   :::image type="content" source="media/how-to-forward-alert-information-to-partners/webhook-extended.png" alt-text="선택 유형 드롭다운 옵션 메뉴에서 webhook 확장 옵션을 선택합니다.":::

1. URL 필드에 엔드포인트 데이터 URL을 추가합니다.

1. (선택 사항) 키 및 값 정의를 사용하여 HTTP 헤더를 사용자 지정합니다. 단추를 선택하여 추가 헤더를 :::image type="icon" source="media/how-to-forward-alert-information-to-partners/add-header.png" border="false"::: 추가합니다.

1. **저장** 을 선택합니다.

웹후크 확장 전달 규칙이 구성되면 관리 콘솔의 전달 화면에서 경고를 테스트할 수 있습니다.

**Webhook 확장 전달 규칙을 테스트하려면:**

1. 관리 콘솔의 왼쪽 창에서 **전달을** 선택합니다.

1. **실행** 단추를 선택하여 경고를 테스트합니다.

    :::image type="content" source="media/how-to-forward-alert-information-to-partners/run-button.png" alt-text="실행 단추를 선택하여 전달 규칙을 테스트합니다.":::

성공 알림이 표시되면 전달 규칙이 작동하는지 알 수 있습니다.

:::image type="content" source="media/how-to-forward-alert-information-to-partners/success.png" alt-text="성공 알림이 표시되면 경고가 올바르게 작동합니다.":::

### <a name="netwitness-action"></a>NetWitness 작업

NetWitness 서버에 경고 정보를 보냅니다.

NetWitness 전달 매개 변수를 정의하려면:

1. NetWitness **호스트 이름** 및 **포트** 정보를 입력합니다.

1. SIEM에서 경고 검색에 대한 타임 스탬프에 대한 표준 시간대를 입력합니다.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="전달 규칙에 표준 시간대를 추가합니다.":::

1. **제출** 을 선택합니다.

### <a name="integrated-vendor-actions"></a>통합된 공급 업체 작업

보안, 디바이스 관리 또는 기타 업계 공급 업체와 시스템을 통합했을 수 있습니다. 이러한 통합을 통해 다음과 같은 작업을 할 수 있습니다.

  - 경고 정보 보내기.

  - 디바이스 인벤토리 정보 보내기.

  - 공급 업체 쪽 방화벽과 통신하기.

통합은 이전에 고수된 보안 솔루션을 형성하고, 디바이스 가시성을 개선하며, 시스템 수준 응답을 가속화하여 위험을 신속하게 완화합니다.

작업 섹션을 사용하여 통합 공급 업체와 통신하는 데 필요한 자격 증명과 기타 정보를 입력합니다.

통합을 위한 전달 규칙을 설정하는 방법에 대한 자세한 내용은 관련 파트너 통합 문서를 참조하세요.

## <a name="test-forwarding-rules"></a>전달 규칙 테스트

센서와 전달 규칙에 정의된 파트너 서버 간의 연결을 테스트합니다.

1. **전달 규칙** 대화 상자에서 규칙을 선택합니다.

1. **자세히** 상자를 선택합니다.

1. **테스트 메시지 보내기** 를 선택합니다.

1. 파트너 시스템으로 이동하여 센서에서 보낸 정보를 받았는지 확인합니다.

## <a name="edit-and-delete-forwarding-rules"></a>전달 규칙 편집 및 삭제 

**전달 규칙을 편집하려면 다음을 수행합니다.**

- **전달 규칙** 화면에서 **자세히** 드롭다운 메뉴 아래 **편집** 을 선택합니다. 원하는 변경을 수행하고 **제출** 을 선택합니다.

**전달 규칙을 제거하려면 다음을 수행합니다.**

- **전달 규칙** 화면에서 **자세히** 드롭다운 메뉴 아래 **제거** 를 선택합니다. **경고** 대화 상자에서 **확인** 을 선택합니다.

## <a name="forwarding-rules-and-alert-exclusion-rules"></a>전달 규칙 및 경고 제외 규칙

관리자가 경고 제외 규칙을 정의했을 수도 있습니다. 이러한 규칙을 통해 관리자는 다양한 매개 변수를 기반으로 센서에 경고 이벤트를 무시하도록 지시하여 경고를 트리거하는 보다 세부적인 제어를 달성할 수 있습니다. 이러한 매개 변수는 디바이스 주소, 경고 이름 또는 특정 센서를 포함할 수도 있습니다.

즉, 사용자가 정의한 전달 규칙은 관리자가 만든 제외 규칙에 따라 무시될 수 있다는 의미입니다. 제외 규칙은 온-프레미스 관리 콘솔에 정의됩니다.

## <a name="see-also"></a>참고 항목

[경고 워크플로 가속화](how-to-accelerate-alert-incident-response.md)
