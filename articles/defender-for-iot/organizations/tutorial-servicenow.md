---
title: Microsoft Defender for IoT와 ServiceNow 통합
description: 이 자습서에서는 Microsoft Defender for IoT와 ServiceNow를 통합하는 방법을 알아봅니다.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/22/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0efdcb780a3050c800c3f955ea3d0742564f5ce8
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132903106"
---
# <a name="tutorial-integrate-servicenow-with-microsoft-defender-for-iot"></a>자습서: Microsoft Defender for IoT와 ServiceNow 통합

> [!Note]
> 이제 ServiceNow 스토어에서 새 [운영 기술 관리자](https://store.servicenow.com/sn_appstore_store.do#!/store/application/31eed0f72337201039e2cb0a56bf65ef/1.1.2?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%25253Btemplate%26q%3Doperational%2520technology%2520manager&sl=sh) 통합을 사용할 수 있습니다. 새로운 통합은 ServiceNow의 OT(운영 기술) 데이터 모델에 대한 Microsoft Defender for IoT 센서 어플라이언스, OT 자산, 네트워크 연결 및 취약성을 간소화합니다.
>
>ServiceNow의 서비스 약관에 대한 ServiceNow의 지원 링크 및 문서를 읽어보세요.
>
>Microsoft Defender for IoT의 현재 통합은 새 통합의 영향을 받지 않으며 Microsoft는 계속 지원합니다.  
>
> 자세한 내용은 ServiceNow 스토어에서 Microsoft Defender for IoT와 [SGC(Service Graph Connector)](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ddd4bf1b53f130104b5cddeeff7b1229) 및 [VR(취약성 대응)](https://store.servicenow.com/sn_appstore_store.do#!/store/application/463a7907c3313010985a1b2d3640dd7e) 통합을 참조하세요.

이 자습서는 Microsoft Defender for IoT와 ServiceNow를 통합하는 방법을 알아보는 데 도움이 됩니다.

Defender for IoT와 ServiceNow 통합은 IoT 및 OT 환경에 대한 새로운 수준의 중앙 집중화된 가시성, 모니터링 및 제어 기능을 제공합니다. 이러한 브리징된 플랫폼을 통해 이전에는 연결할 수 없었던 ICS 및 IoT 디바이스에 대한 자동 디바이스 가시성과 위협 관리 기능을 사용할 수 있습니다.

ServiceNow CMDB(구성 관리 데이터베이스)는 Defender for IoT 플랫폼에서 푸시하는 다양한 디바이스 특성 세트를 사용하여 보강되고 보완됩니다. 이는 디바이스 환경에 대한 포괄적이고 지속적인 가시성을 보장합니다. 이러한 가시성을 통해 단일 창에서 모니터링하고 응답할 수 있습니다. 

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * ServiceNow에서 Defender for IoT 애플리케이션 다운로드
> * ServiceNow와 통신하도록 Defender for IoT 설정
> * ServiceNow에서 액세스 토큰 만들기
> * Defender for IoT 디바이스 특성을 ServiceNow에 보내기
> * HTTPS 프록시를 사용하여 통합 설정
> * ServiceNow에서 Defender for IoT 검색 결과 보기
> * 연결된 장치 보기

## <a name="prerequisites"></a>필수 구성 요소

### <a name="software-requirements"></a>소프트웨어 요구 사항

ServiceNow 및 Defender for IoT에 대한 액세스 

- ServiceNow Service Management 버전 3.0.2

- Defender for IoT 패치 2.8.11.1 이상

> [!Note]
> 이미 Defender for IoT와 ServiceNow 통합을 사용하다가 온-프레미스 관리 콘솔을 사용하여 업그레이드하는 경우 이전에 Defender for IoT 센서에서 수신한 데이터를 ServiceNow에서 삭제해야 합니다.

### <a name="architecture"></a>Architecture

- **온-프레미스 관리 콘솔 아키텍처**: 하나의 ServiceNow 인스턴스와 통신하도록 온-프레미스 관리 콘솔을 설정합니다. 온-프레미스 관리 콘솔은 REST API를 사용하여 센서 데이터를 Defender for IoT 애플리케이션에 푸시합니다.

    온-프레미스 관리 콘솔을 사용하도록 시스템을 설정하려면 설정된 센서에서 ServiceNow 동기화, 전달 규칙 및 프록시 구성을 사용하지 않도록 설정해야 합니다.

- **센서 아키텍처**: 센서와 ServiceNow 간의 직접 통신을 포함하도록 환경을 설정하려면 각 센서에 대해 ServiceNow 동기화, 전달 규칙 및 프록시 구성(프록시가 필요한 경우)을 정의합니다.

## <a name="download-the-defender-for-iot-application-in-servicenow"></a>ServiceNow에서 Defender for IoT 애플리케이션 다운로드

ServiceNow 내에서 Defender for IoT 애플리케이션에 액세스하려면 ServiceNow 애플리케이션 스토어에서 애플리케이션을 다운로드해야 합니다. 

**ServiceNow에서 Defender for IoT 애플리케이션에 액세스하려면**:

1. [ServiceNow 애플리케이션 스토어](https://store.servicenow.com/)로 이동합니다.

1. `Defender for IoT` 또는 `CyberX IoT/ICS Management`를 검색합니다.

   :::image type="content" source="media/tutorial-servicenow/search-results.png" alt-text="ServiceNow 검색 화면의 스크린샷":::

1. 애플리케이션을 선택합니다.

   :::image type="content" source="media/tutorial-servicenow/cyberx-app.png" alt-text="검색 화면 결과의 스크린샷":::

1. **앱 요청** 을 선택합니다.

   :::image type="content" source="media/tutorial-servicenow/sign-in.png" alt-text="자격 증명을 사용하여 애플리케이션에 로그인합니다.":::

1. 로그인하고 애플리케이션을 다운로드합니다.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>ServiceNow와 통신하도록 Defender for IoT 설정

이 문서에서는 경고 정보를 ServiceNow 테이블에 푸시하도록 Defender for IoT를 구성합니다. Defender for IoT 경고는 ServiceNow에 보안 인시던트로 표시됩니다. 이는 경고 정보를 ServiceNow에 보내는 Defender for IoT 전달 규칙을 정의하여 수행할 수 있습니다.

**경고 정보를 ServiceNow 테이블에 푸시하려면**:

1. 온-프레미스 관리 콘솔에 로그인합니다.

1. 왼쪽 창에서 **전달** 을 선택합니다.

1. :::image type="icon" source="media/tutorial-servicenow/plus-icon.png" border="false"::: 단추를 선택합니다.

    :::image type="content" source="media/tutorial-servicenow/forwarding-rule.png" alt-text="전달 규칙 만들기 창의 스크린샷":::

1. 규칙 이름을 추가합니다.

1. Defender for IoT가 전달 규칙을 트리거하는 조건을 정의합니다. 전달 규칙 조건을 사용하면 Defender for IoT에서 ServiceNow로 전송되는 정보의 양을 파악하고 관리하는 데 도움이 됩니다. 다음 옵션을 사용할 수 있습니다.

    - **심각도 수준:** 전달해야 하는 최소 보안 수준 인시던트입니다. 예를 들어 **경미** 를 선택하면 경미한 경고 및 이 심각도 수준 이상의 모든 경고가 전달됩니다. 수준은 Defender for IoT에 의해 미리 정의됩니다.

    - **프로토콜:** 검색된 트래픽이 특정 프로토콜을 통해 실행되고 있는 경우에만 전달 규칙을 트리거합니다. 드롭다운 목록에서 필요한 프로토콜을 선택하거나 모두 선택합니다.

    - **엔진:** 필요한 엔진을 선택하거나 모두 선택합니다. 선택한 엔진의 경고가 전송됩니다.

1. **경고 알림 보고** 가 선택되어 있는지 확인합니다.

1. 작업 섹션에서 **추가** 를 선택한 다음 **ServiceNow** 를 선택합니다.

    :::image type="content" source="media/tutorial-servicenow/select-servicenow.png" alt-text="드롭다운 옵션에서 ServiceNow를 선택합니다.":::

1. ServiceNow 작업 매개 변수를 입력합니다.

    :::image type="content" source="media/tutorial-servicenow/parameters.png" alt-text="ServiceNow 작업 매개 변수 입력":::

1. **작업** 창에서 다음 매개 변수를 설정합니다.

      | 매개 변수 | 설명 |
      |--|--|
      | 도메인 | ServiceNow 서버 IP 주소를 입력합니다. |
      | 사용자 이름 | ServiceNow 서버 사용자 이름을 입력합니다. |
      | 암호 | ServiceNow 서버 암호를 입력합니다. |
      | 클라이언트 ID | ServiceNow의 **애플리케이션 레지스트리** 페이지에서 Defender for IoT용으로 받은 클라이언트 ID를 입력합니다. |
      | 클라이언트 암호 | ServiceNow의 **애플리케이션 레지스트리** 페이지에서 Defender for IoT용으로 만든 클라이언트 비밀 문자열을 입력합니다. |
      | 보고서 유형 | **인시던트**: ServiceNow에 표시되는 경고의 목록을 각 경고의 인시던트 ID 및 간단한 설명과 함께 전달합니다.<br /><br />**Defender for IoT 애플리케이션**: 센서 정보, 엔진, 원본 및 대상 주소를 포함한 전체 경고 정보를 전달합니다. 이 정보는 ServiceNow 애플리케이션에서 Defender for IoT로 전달됩니다. |

1. **SAVE**(저장)를 선택합니다. 

Defenders for IoT 경고는 이제 ServiceNow에서 인시던트로 표시됩니다.

## <a name="create-access-tokens-in-servicenow"></a>ServiceNow에서 액세스 토큰 만들기

ServiceNow에서 Defender for IoT와 통신할 수 있도록 하려면 토큰이 필요합니다.

Defender for IoT 전달 규칙을 만들 때 입력한 `Client ID` 및 `Client Secret`이 필요합니다. 전달 규칙은 경고 정보를 ServiceNow에 전달하고, Defender for IoT에서 디바이스 특성을 ServiceNow 테이블에 푸시하도록 구성할 때 사용합니다.

## <a name="send-defender-for-iot-device-attributes-to-servicenow"></a>Defender for IoT 디바이스 특성을 ServiceNow에 보내기

광범위한 디바이스 특성을 ServiceNow 테이블에 푸시하도록 Defender for IoT를 구성합니다. ServiceNow로 특성을 보내려면 온-프레미스 관리 콘솔을 ServiceNow 인스턴스에 매핑해야 합니다. 이렇게 하면 Defender for IoT 플랫폼이 해당 인스턴스와 통신하고 인증할 수 있습니다.

**ServiceNow 인스턴스를 추가하려면**:

1. Defender for IoT 온-프레미스 관리 콘솔에 로그인합니다.

1. 온-프레미스 관리 콘솔 통합 섹션에서 **시스템 설정**, **ServiceNow** 를 차례로 선택합니다.

      :::image type="content" source="media/tutorial-servicenow/servicenow.png" alt-text="ServiceNow 선택 단추의 스크린샷":::

1. ServiceNow 동기화 대화 상자에 다음 동기화 매개 변수를 입력합니다.

    :::image type="content" source="media/tutorial-servicenow/sync.png" alt-text="ServiceNow 동기화 대화 상자의 스크린샷":::

     매개 변수 | 설명 |
    |--|--|
    | 동기화 사용 | 매개 변수를 정의한 후 동기화를 사용하거나 사용하지 않도록 설정합니다. |
    | 도기화 빈도(분) | 기본적으로 정보는 ServiceNow로 60분마다 푸시됩니다. 최솟값은 5분입니다. |
    | ServiceNow 인스턴스 | ServiceNow 인스턴스 URL을 입력합니다. |
    | 클라이언트 ID | ServiceNow의 **애플리케이션 레지스트리** 페이지에서 Defender for IoT용으로 받은 클라이언트 ID를 입력합니다. |
    | 클라이언트 암호 | ServiceNow의 **애플리케이션 레지스트리** 페이지에서 Defender for IoT용으로 만든 클라이언트 비밀 문자열을 입력합니다. |
    | 사용자 이름 | 이 인스턴스의 사용자 이름을 입력합니다. |
    | 암호 | 이 인스턴스의 암호를 입력합니다. |

1. **SAVE**(저장)를 선택합니다.

'마지막 동기화' 날짜를 검토하여 온-프레미스 관리 콘솔이 ServiceNow 인스턴스에 연결되어 있는지 확인합니다.

:::image type="content" source="media/tutorial-servicenow/sync-confirmation.png" alt-text="마지막 동기화를 보고 발생하는 통신의 스크린샷":::

## <a name="set-up-the-integrations-using-an-https-proxy"></a>HTTPS 프록시를 사용하여 통합 설정

Defender for IoT와 ServiceNow 통합을 위해 Defender를 설정하는 경우 온-프레미스 관리 콘솔과 ServiceNow 서버는 포트 443을 사용하여 통신합니다. ServiceNow 서버가 프록시 뒤에 있는 경우 기본 포트를 사용할 수 없습니다.

Defender for IoT는 통합에 사용되는 기본 포트를 변경할 수 있도록 하여 ServiceNow 통합에서 HTTPS 프록시를 지원합니다.

**프록시를 구성하려면**:

1. 다음 명령을 사용하여 온-프레미스 관리 콘솔에서 글로벌 속성을 편집합니다.

    ```bash
    sudo vim /var/cyberx/properties/global.properties
    ```

2. 다음 매개 변수를 추가합니다.

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. **저장하고 끝내기** 를 선택합니다.

4. 다음 명령을 사용하여 온-프레미스 관리 콘솔을 다시 설정합니다. 

    ```bash
    sudo monit restart all
    ```

구성이 설정되면 모든 ServiceNow 데이터가 구성된 프록시를 통해 전달됩니다.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>ServiceNow에서 Defender for IoT 검색 결과 보기

이 문서에서는 ServiceNow에 표시되는 디바이스 특성 및 경고 정보에 대해 설명합니다.

**디바이스 특성을 보려면**:

1. ServiceNow에 로그인합니다.

2. **CyberX 플랫폼** 으로 이동합니다.

3. **인벤토리** 또는 **경고** 로 이동합니다.

   [:::image type="content" source="media/tutorial-servicenow/alert-list.png" alt-text="인벤토리 또는 경고의 스크린샷":::](media/tutorial-servicenow/alert-list.png#lightbox)

## <a name="view-connected-devices"></a>연결된 장치 보기

연결된 디바이스를 보려면

1. 디바이스를 선택한 다음, 해당 디바이스에 대해 나열된 **어플라이언스** 를 선택합니다.

    :::image type="content" source="media/tutorial-servicenow/appliance.png" alt-text="목록에서 원하는 어플라이언스의 스크린샷":::

1. **디바이스 세부 정보** 대화 상자에서 **연결된 디바이스** 를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

정리할 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 ServiceNow 통합을 시작하는 방법을 알아보았습니다. 계속하여 [Cisco 통합](./tutorial-forescout.md)에 대해 알아보세요.

> [!div class="nextstepaction"]
> [다음 단계 단추](./tutorial-forescout.md)
