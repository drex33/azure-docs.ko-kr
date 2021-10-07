---
title: Azure Defender for IoT와 QRadar 통합
description: 이 자습서에서는 QRadar를 Azure Defender for IoT와 통합하는 방법을 알아봅니다.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/12/2021
ms.custom: template-tutorial
ms.openlocfilehash: ec55a652b59f7d45e01ef22e62bd046473ef8559
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124839231"
---
# <a name="tutorial-integrate-qradar-with-azure-defender-for-iot"></a>자습서: Azure Defender for IoT와 QRadar 통합

이 자습서를 사용하면 QRadar를 Azure Defender for IoT와 통합하고 사용하는 방법을 알아볼 수 있습니다.

Defender for IoT는 특허 받은 ICS 인식 위협 분석 및 기계 학습을 사용하는 유일한 ICS 및 IoT 사이버 보안 플랫폼입니다.

Defender for IoT는 연속 ICS 위협 모니터링 플랫폼을 IBM QRadar와 통합했습니다.

이러한 통합에 따른 몇 가지 이점은 다음과 같습니다.

- Azure Defender for IoT 알림을 IBM QRadar로 전달하여 IT 통합, OT 보안 모니터링 및 거버넌스 이점을 얻을 수 있습니다.

- IT 및 OT 환경 모두에 대한 개요를 얻을 수 있습니다. 이를 통해 IT 및 OT 경계를 자주 넘나드는 다단계 공격을 탐지하고 대응할 수 있습니다.

- 기존 SOC 워크플로와 통합됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * QRadar용 Syslog 수신기 구성
> * Defender for IoT 플랫폼 QID 배포
> * QRadar 전달 규칙 설정
> * 관리 콘솔에서 QRadar에 알림 매핑
> * 경고에 사용자 지정 필드 추가

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 대한 필수 구성 요소가 없습니다.

## <a name="configure-syslog-listener-for-qradar"></a>QRadar용 Syslog 수신기 구성

**QRadar에서 작동하도록 Syslog 수신기를 구성** 하려면 다음을 수행합니다.

1. QRadar에 로그인합니다.

1. 왼쪽 창에서 **관리자** > **데이터 원본** 을 선택합니다.

1. 데이터 원본 창에서 **로그 원본** 을 선택합니다.

   [:::image type="content" source="media/tutorial-qradar/log.png" alt-text="사용 가능한 옵션에서 로그 원본을 선택하는 스크린샷":::](media/tutorial-qradar/log.png#lightbox)

1. **모달** 창에서 **추가** 를 선택합니다.

    [:::image type="content" source="media/tutorial-qradar/modal.png" alt-text="Syslog를 선택한 후에 열린 모달 창의 스크린샷":::](media/tutorial-qradar/modal.png#lightbox)

1. **로그 원본 추가** 대화 상자에서 다음 매개 변수를 설정합니다.

    :::image type="content" source="media/tutorial-qradar/source.png" alt-text="적절한 필드를 채워 로그 원본을 추가하는 스크린샷":::

   - **로그 원본 이름**: `<Sensor name>`

   - **로그 원본 설명**: `<Sensor name>`

   - **로그 원본 유형**: `Universal LEEF`

   - **프로토콜 구성**: `Syslog`

   - **로그 원본 식별자**: `<Sensor name>`

   > [!NOTE]
   > 로그 원본 식별자 이름은 공백을 포함할 수 없습니다. 각 공백 문자를 밑줄로 바꾸는 것이 좋습니다.

1. **저장** 을 선택합니다.

1. **변경 내용 배포** 를 선택합니다.

   :::image type="content" source="media/tutorial-qradar/deploy.png" alt-text="변경 내용 배포 보기의 스크린샷":::

## <a name="deploy-defender-for-iot-platform-qid"></a>Defender for IoT 플랫폼 QID 배포

QID는 QRadar의 이벤트 식별자입니다. 모든 Defenders for IoT 플랫폼 보고서가 동일한 이벤트(센서 경고) 아래에 태그로 지정됩니다.

**Defender for IoT 플랫폼 QID를 배포** 하려면 다음을 수행합니다.

1. QRadar 콘솔에 로그인합니다.

1. `xsense_qids`라는 파일을 만듭니다.

1. 파일에서 다음 명령어 사용: `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`.

1. 실행: `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`. QID가 성공적으로 배포되었다는 메시지가 표시됩니다.

## <a name="setup-qradar-forwarding-rules"></a>QRadar 전달 규칙 설정

통합이 작동하려면 Defender for IoT 어플라이언스에서 QRadar 전달 규칙을 설정해야 합니다.

**Defender for IoT 어플라이언스에서 QRadar 알림을 정의하려면 다음 안내를 따르세요**.

1. 측면 메뉴에서 **전달** 을 선택합니다.

1. **전달 규칙 만들기** 를 선택합니다.

1. 작업을 **QRadar** 로 설정합니다.

    :::image type="content" source="media/tutorial-qradar/create.png" alt-text="전달 규칙 만들기 창의 스크린샷":::

1. QRadar IP 주소 및 시간대를 구성합니다.

1. **제출** 을 선택합니다.

## <a name="map-notifications-to-qradar"></a>QRadar에 알림 매핑

다음으로, 온-프레미스 관리 콘솔에서 규칙을 매핑해야 합니다.

**알림을 QRadar에 매핑** 하려면 다음을 수행합니다.

1. 관리 콘솔에 로그인합니다.

1. 왼쪽 창에서 **전달** 을 선택합니다.

1. Qradar GUI의 QRadar에서 **로그 활동** 을 선택합니다.

1. **필터 추가** 를 선택하고 다음 매개 변수를 설정합니다.
   - 매개 변수: `Log Sources [Indexed]`
   - 연산자: `Equals`
   - 로그 원본 그룹: `Other`
   - 로그 원본: `<Xsense Name>`

1. 센서에서 알 수 없는 보고서를 두 번 클릭합니다.

1. **맵 이벤트** 를 선택합니다.

1. 모달 로그 원본 이벤트 페이지에서 다음과 같이 선택합니다.
   - 상위 수준 범주 - 의심스러운 활동 + 하위 수준 범주 - 알 수 없는 의심스러운 이벤트 + 로그
   - 원본 유형 - 모두

1. **검색** 을 선택합니다.

1. 결과에서 XSense 이름이 표시된 줄을 선택하고 **확인** 을 선택합니다.

지금부터 모든 센서 보고서에는 센서 경고라는 태그가 지정됩니다.

## <a name="add-custom-fields-to-the-alerts"></a>경고에 사용자 지정 필드 추가

**사용자 지정 필드를 경고에 추가하려면 다음 안내를 따르세요**.

1. **속성 추출** 을 선택합니다.

1. **Regex 기반** 을 선택합니다.

1. 다음 필드를 구성합니다.
   - 새 속성: _아래 목록에서 선택_
      - 센서 경고 설명
      - 센서 경고 ID
      - 센서 경고 점수
      - 센서 경고 제목
      - 센서 대상 이름
      - 센서 직접 리디렉션
      - 센서 발신자 IP
      - 센서 발신자 이름
      - 센서 경고 엔진
      - 센서 원본 디바이스 이름
   - **구문 분석 최적화** 확인
   - 필드 형식: `AlphaNumeric`
   - **사용** 확인
   - 로그 원본 유형: `Universal LEAF`
   - 로그 원본: `<Sensor Name>`
   - 이벤트 이름(이미 센서 경고로 설정되어 있어야 함)
   - 캡처 그룹: 1
   - Regex:
      - 센서 경고 설명 정규식: `msg=(.*)(?=\t)`
      - 센서 경고 ID 정규식: `alertId=(.*)(?=\t)`
      - 센서 경고 점수 정규식: `Detected score=(.*)(?=\t)`
      - 센서 경고 제목 정규식: `title=(.*)(?=\t)`
      - 센서 대상 이름 정규식: `dstName=(.*)(?=\t)`
      - 센서 직접 리디렉션 정규식: `rta=(.*)(?=\t)`
      - 센서 발신자 IP: 정규식: `reporter=(.*)(?=\t)`
      - 센서 발신자 이름 정규식: `senderName=(.*)(?=\t)`
      - 센서 경고 엔진 정규식: `engine =(.*)(?=\t)`
      - 센서 원본 디바이스 이름 정규식: `src`

## <a name="clean-up-resources"></a>리소스 정리

정리할 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 QRadar 통합을 시작하는 방법을 알아보았습니다. [Azure Defender for IoT와 ServiceNow를 통합](tutorial-servicenow.md)하는 방법을 계속 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Defender for IoT와 ServiceNow 통합](tutorial-servicenow.md)
