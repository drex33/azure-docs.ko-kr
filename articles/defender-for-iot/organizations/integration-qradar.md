---
title: QRadar 통합
description: QRadar와 Defender for IoT 솔루션 통합을 구성합니다.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 26808482ad9fabd528d2ec7e0d846c301c2c2ebf
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342422"
---
# <a name="about-the-qradar-integration"></a>QRadar 통합 정보

Defender for IoT는 중요한 국가 인프라를 방어한 실적을 가진 블루 팀 전문가가 빌드한 유일한 ICS 및 IoT 사이버 보안 플랫폼과 특허받은 ICS 인식 위협 분석 및 기계 학습을 갖춘 유일한 플랫폼을 제공합니다.

Defender for IoT는 연속 ICS 위협 모니터링 플랫폼을 IBM QRadar와 통합했습니다. 

이러한 통합에 따른 몇 가지 이점은 다음과 같습니다.

- Azure Defender for IoT 알림을 IBM QRadar로 전달하여 IT 통합, OT 보안 모니터링 및 거버넌스 이점을 얻을 수 있습니다.

- IT 및 OT 환경 모두 조감도 보기를 이용할 수 있습니다. 이를 통해 종종 IT 및 OT 경계를 벗어나는 다단계 공격을 감지하고 대응할 수 있습니다.

- 기존 SOC 워크플로와 통합됩니다.

## <a name="configuring-syslog-listener-for-qradar"></a>QRadar를 위한 Syslog 수신기 구성

QRadar와 함께 작동하도록 Syslog 수신기를 구성하려면 다음 안내를 따르세요.

1. QRadar에 로그인합니다.

1. 왼쪽 창에서 **관리자** > **데이터 원본** 을 선택합니다.

   [:::image type="content" source="media/integration-qradar/log.png" alt-text="사용 가능한 옵션 중에서 로그 원본을 선택합니다.":::](media/integration-qradar/log.png#lightbox)

1. 데이터 원본 창에서 **로그 원본** 을 선택합니다.

   [:::image type="content" source="media/integration-qradar/modal.png" alt-text="Syslog를 선택한 후 모델 창이 열립니다.":::](media/integration-qradar/modal.png#lightbox)

1. **모달** 창에서 **추가** 를 선택합니다.

   :::image type="content" source="media/integration-qradar/source.png" alt-text="적절한 필드를 입력하여 로그 원본을 추가합니다.":::

1. **로그 원본 추가** 대화 상자에서 다음 매개 변수를 설정합니다.

   - **로그 원본 이름**: `<XSense Name>`
   
   - **로그 원본 설명**: `<XSense Name>`
   
   - **로그 원본 유형**: `Universal LEEF`

   - **프로토콜 구성**: `Syslog`
   
   - **로그 원본 식별자**: `<XSenseName>`
   
   > [!NOTE]
   > 로그 원본 식별자 이름은 공백을 포함할 수 없습니다. 각 공백 문자를 밑줄로 바꾸는 것이 좋습니다.

1. **저장** 을 선택합니다.

1. **변경 내용 배포** 를 선택합니다.

:::image type="content" source="media/integration-qradar/deploy.png" alt-text="변경 내용 배포 보기의 스크린샷":::

## <a name="deploying-defender-for-iot-platform-qid"></a>Defender for IoT 플랫폼 QID 배포

QID는 QRadar의 이벤트 식별자입니다. 모든 Defenders for IoT 플랫폼 보고서가 동일한 이벤트 아래에 태그로 지정됩니다(XSense 경고).

**Xsense QID를 배포하려면 다음 안내를 따르세요**.

1. QRadar 콘솔에 로그인합니다.

1. `xsense_qids`라는 파일을 만듭니다.

1. 파일에서 다음 명령어 사용: `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`.

1. 실행: `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`. QID가 성공적으로 배포되었다는 메시지가 표시됩니다.

## <a name="setting-up-qradar-forwarding-rules"></a>QRadar 전달 규칙 설정

Defender for IoT 어플라이언스에서 Qradar 전달 규칙을 구성합니다. 온-프레미스 관리 콘솔에서 규칙을 매핑합니다.

**Defender for IoT 어플라이언스에서 QRadar 알림을 정의하려면 다음 안내를 따르세요**.

1. 측면 메뉴에서 **전달** 을 선택합니다.

   :::image type="content" source="media/integration-qradar/create.png" alt-text="전달 규칙을 만듭니다.":::

1. 작업을 **QRadar** 로 설정합니다.

1. QRadar IP 주소 및 시간대를 구성합니다.

1. **제출** 을 선택합니다.

**중앙 관리자에서 QRadar에 알림을 매핑하려면 다음 안내를 따르세요**.

1. 측면 메뉴에서 **전달** 을 선택합니다.

1. Qradar GUI의 QRadar에서 **로그 활동** 을 선택합니다.

1. **필터 추가** 를 선택하고 다음 매개 변수를 설정합니다.
   - 매개 변수: `Log Sources [Indexed]`
   - 연산자: `Equals`
   - 로그 원본 그룹: `Other`
   - 로그 원본: `<Xsense Name>`

1. XSense에서 알 수 없는 보고서를 두 번 클릭합니다.

1. **맵 이벤트** 를 선택합니다.

1. 모달 로그 원본 이벤트 페이지에서 다음과 같이 선택합니다.
   - 상위 수준 범주 - 의심스러운 활동 + 하위 수준 범주 - 알 수 없는 의심스러운 이벤트 + 로그
   - 원본 유형 - 모두

1. **검색** 을 선택합니다.

1. 결과에서 XSense 이름이 표시된 줄을 선택하고 **확인** 을 선택합니다.

이제부터 모든 XSense 보고서가 XSense 경고로 태그 지정됩니다.

## <a name="adding-custom-fields-to-alerts"></a>경고에 사용자 지정 필드 추가

**사용자 지정 필드를 경고에 추가하려면 다음 안내를 따르세요**.

1. **속성 추출** 을 선택합니다.

1. **Regex 기반** 을 선택합니다.

1. 다음 필드를 구성합니다.
   - 새 속성: _아래 목록에서 선택_
      - Xsense 경고 설명
      - Xsense 경고 ID
      - Xsense 경고 점수
      - Xsense 경고 제목
      - Xsense 대상 이름
      - Xsense 직접 리디렉션
      - Xsense 보낸 사람 IP
      - Xsense 보낸 사람 이름
      - Xsense 경고 엔진
      - Xsense 원본 디바이스 이름
   - **구문 분석 최적화** 확인
   - 필드 형식: `AlphaNumeric`
   - **사용** 확인
   - 로그 원본 유형: `Universal LEAF`
   - 로그 원본: `<Xsense Name>`
   - 이벤트 이름(이미 XSense 경고로 설정되어야 함)
   - 캡처 그룹: 1
   - Regex:
      - Xsense 경고 설명 RegEx: `msg=(.*)(?=\t)`
      - Xsense 경고 ID RegEx: `alertId=(.*)(?=\t)`
      - Xsense 경고 점수 RegEx: `Detected score=(.*)(?=\t)`
      - Xsense 경고 제목 RegEx: `title=(.*)(?=\t)`
      - Xsense 대상 이름 RegEx: `dstName=(.*)(?=\t)`
      - Xsense 직접 리디렉션 RegEx: `rta=(.*)(?=\t)`
      - Xsense 보낸 사람 IP: RegEx: `reporter=(.*)(?=\t)`
      - Xsense 보낸 사람 이름 RegEx: `senderName=(.*)(?=\t)`
      - Xsense 경고 엔진 RegEx: `engine =(.*)(?=\t)`
      - Xsense 원본 디바이스 이름 RegEx: `src`

## <a name="defining-defender-for-iot-appliance-name"></a>Defender for IoT 어플라이언스 이름 정의

언제든지 플랫폼 이름을 변경할 수 있습니다.

사이트를 구축하고 온-프레미스 관리 콘솔에서 영역에 어플라이언스를 할당할 때는 각 어플라이언스에 특별한 이름을 할당해야 합니다. 예를 들어 “Motorcycles PL Unit 2”는 이 어플라이언스가 오토바이 생산 라인에서 2번 유닛을 보호하고 있음을 의미합니다. 

어플라이언스 이름이 로그에 전달되기 때문에 어플라이언스에 대해 의미 있는 이름을 선택하는 것이 중요합니다. 로그를 검토할 때는 각 경고에 센서가 연결됩니다. 이름을 기반으로 각 경고와 연결된 센서를 식별할 수 있습니다.

**어플라이언스 이름을 변경하려면 다음 안내를 따르세요**.

1. 측면 메뉴에서 현재 어플라이언스 이름을 선택합니다. **관리 콘솔 구성 편집** 대화 상자가 나타납니다.

   :::image type="content" source="media/integration-qradar/edit-management-console.png" alt-text="콘솔 이름을 변경합니다.":::

1. 이름 필드에 이름을 입력하고 **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

[경고 정보를 전달](how-to-forward-alert-information-to-partners.md)하는 방법을 알아봅니다.
