---
title: Azure Logic Apps를 사용하여 IoT 원격 모니터링 및 알림 | Microsoft Docs
description: Azure Logic Apps을 사용하여 IoT Hub에서 IoT 온도를 모니터링하고 감지된 이상에 대한 전자 메일 알림을 자동으로 사서함에 보낼 수 있습니다.
author: eross-msft
keywords: iot 모니터링, iot 알림, iot 온도 모니터링
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.openlocfilehash: 28b0c6720145ce6591f4f62cc89de17907194cf3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552549"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Azure Logic Apps으로 IoT Hub와 사서함을 연결하여 IoT 원격 모니터링 및 알림

![엔드투엔드 다이어그램](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml)는 온-프레미스 서비스, 클라우드 서비스, 하나 이상의 기업과 여러 프로토콜 간에 워크플로를 오케스트레이션하도록 지원합니다. 논리 앱은 트리거로 시작하고, 그 뒤에 조건 및 반복기 등과 같은 기본 제공 컨트롤을 사용하여 순차화할 수 있는 하나 이상의 작업이 이어집니다. 이와 같은 유연성 덕분에 Logic Apps는 IoT 모니터링 시나리오를 위한 이상적인 IoT 솔루션입니다. 예를 들어, 디바이스의 원격 분석 데이터가 IoT Hub 엔드포인트에 도착하면 논리 앱 워크플로를 시작하여 Azure Storage Blob에서 데이터를 웨어하우징하고, 데이터 이상을 경고하는 메일 경고를 보내고, 디바이스에서 오류를 보고하는 경우 기술자 방문을 예약하는 등의 작업을 수행할 수 있습니다.

이 문서에서는 IoT 허브와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 만드는 방법을 알아봅니다. 디바이스에서 실행되는 클라이언트 코드는 IoT 허브에 보내는 모든 원격 분석 메시지에서 애플리케이션 속성 `temperatureAlert`를 설정합니다. 클라이언트 코드가 30C를 초과하는 온도를 검색하는 경우 이 속성을 `true`로 설정하고, 그러지 않는 경우 이 속성을 `false`로 설정합니다.

IoT 허브에 도착하는 메시지는 다음과 유사하여, 본문에 원격 분석 데이터가 포함되어 있고 `temperatureAlert` 속성은 애플리케이션 속성에 포함되어 있습니다(시스템 속성은 표시되지 않음).

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

IoT 허브 메시지 형식에 대한 자세한 내용은 [IoT 허브 메시지 만들기 및 읽기](iot-hub-devguide-messages-construct.md)를 참조하세요.

이 항목에서는 `temperatureAlert` 속성이 `true`로 설정된 경우 Service Bus 엔드포인트로 메시지를 보내도록 IoT 허브에서 라우팅을 설정합니다. 그런 다음 Service Bus 엔드포인트에 도착하는 메시지에 따라 트리거하여 나에게 메일 알림을 보내는 논리 앱을 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 디바이스 자습서 중 하나를 완료합니다. 예를 들어 [node.js를 사용하는 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)로 이동하거나 [원격 분석 전송](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) 빠른 시작 중 하나로 이동할 수 있습니다. 이러한 문서는 다음 요구 사항을 다룹니다.

  * 활성화된 Azure 구독.
  * 구독 중인 Azure IoT Hub
  * 디바이스에서 실행 중인, Azure IoT Hub에 원격 분석 메시지를 보내는 클라이언트 애플리케이션

## <a name="create-service-bus-namespace-and-queue"></a>Service Bus 네임스페이스 및 큐 만들기

Service Bus 네임스페이스 및 큐를 만듭니다. 이 항목의 뒷부분에서는 온도 경고가 포함된 메시지를 Service Bus 큐로 보내는 라우팅 규칙을 IoT 허브에서 만듭니다. 여기서 메시지는 논리 앱으로 선택되고 알림 메일을 보내도록 논리 앱을 트리거합니다.

### <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **+ 리소스 만들기** > **통합** > **Service Bus** 를 선택합니다.

1. **네임스페이스 만들기** 창에서 다음 정보를 제공합니다.

   **이름**: Service Bus 네임스페이스 이름입니다. 네임스페이스는 Azure에서 고유해야 합니다.

   **가격 책정 계층**: 드롭다운 목록에서 **기본** 을 선택합니다. 이 자습서에서는 기본 계층이면 충분합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: IoT Hub에서 사용하는 것과 같은 위치를 사용합니다.

   ![Azure Portal에서 Service Bus 네임스페이스 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. **만들기** 를 선택합니다. 배포가 완료될 때까지 기다렸다가 다음 단계로 진행합니다.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>네임스페이스에 Service Bus 큐 추가

1. Service Bus 네임스페이스를 엽니다. Service Bus 네임스페이스를 가져오는 가장 쉬운 방법은 리소스 창에서 **리소스 그룹** 을 선택하고 리소스 그룹을 선택한 다음, 리소스 목록에서 Service Bus 네임스페이스를 선택하는 것입니다.

1. **Service Bus 네임스페이스** 창에서 **+ 큐** 를 선택합니다.

1. 큐 이름을 입력한 다음 **만들기** 를 선택합니다. 큐가 생성되면 **큐 만들기** 창이 닫힙니다.

   ![Azure Portal에서 Service Bus 큐 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. **Service Bus 네임스페이스** 창으로 돌아가 **엔터티** 아래에서 **큐** 를 선택합니다. 목록에서 Service Bus 큐를 연 다음, **공유 액세스 정책** >  **+ 추가** 를 선택합니다.

1. 정책 이름을 입력하고, **관리** 를 선택한 다음 **만들기** 를 클릭합니다.

   ![Azure Portal에서 Service Bus 큐 정책 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>IoT 허브에 사용자 지정 엔드포인트와 라우팅 규칙 추가

Service Bus 큐에 대한 사용자 지정 엔드포인트를 IoT 허브에 추가하고 온도 경고가 포함된 메시지를 해당 엔드포인트에 전달하는 메시지 라우팅 규칙을 만듭니다. 여기서 메시지는 논리 앱에서 선택합니다. 라우팅 규칙은 라우팅 쿼리 `temperatureAlert = "true"`를 사용하여 디바이스에서 실행 중인 클라이언트 코드로 설정되는 `temperatureAlert` 애플리케이션 속성의 값에 따라 메시지를 전달합니다. 자세히 알아보려면 [메시지 속성에 따른 메시지 라우팅 쿼리](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties)를 참조하세요.

### <a name="add-a-custom-endpoint"></a>사용자 지정 엔드포인트 추가

1. IoT Hub를 엽니다. IoT 허브로 가져오는 가장 쉬운 방법은 리소스 창에서 **리소스 그룹** 을 선택하고 리소스 그룹을 선택한 다음, 리소스 목록에서 IoT 허브를 선택하는 것입니다.

1. **메시징** 에서 **메시지 라우팅** 을 선택합니다. **메시지 라우팅** 창에서 **사용자 지정 엔드포인트** 탭을 선택한 다음, **+ 추가** 를 선택합니다. 드롭다운 목록에서 **Service Bus 큐** 를 선택합니다.

   ![Service Bus 큐 옵션이 강조 표시된 스크린샷](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. **Service Bus 엔드포인트 추가** 창에서 다음 정보를 입력합니다.

   **엔드포인트 이름**: 엔드포인트의 이름입니다.

   **Service Bus 네임스페이스**: 앞에서 만든 네임스페이스를 선택합니다.

   **Service Bus 큐**: 앞에서 만든 큐를 선택합니다.

   ![Azure Portal에서 IoT Hub에 엔드포인트 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. **만들기** 를 선택합니다. 엔드포인트가 생성되면 다음 단계로 진행합니다.

### <a name="add-a-routing-rule"></a>라우팅 규칙 추가

1. **메시지 라우팅** 창으로 다시 돌아가 **경로** 탭을 선택한 다음, **+ 추가** 를 선택합니다.

1. **경로 추가** 창에서 다음 정보를 입력합니다.

   **이름**: 라우팅 규칙의 이름입니다.

   **엔드포인트**: 앞에서 만든 엔드포인트를 선택합니다.

   **데이터 원본**: **디바이스 원격 분석 메시지** 를 선택합니다.

   **라우팅 쿼리**: `temperatureAlert = "true"`를 입력합니다.

   ![Azure Portal에서 라우팅 규칙 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. **저장** 을 선택합니다. **메시지 라우팅** 창을 닫을 수 있습니다.

## <a name="create-and-configure-a-logic-app"></a>논리 앱 만들기 및 구성

이전 섹션에서는 온도 경고가 포함된 메시지를 Service Bus 큐로 라우팅하도록 IoT 허브를 설정했습니다. 이제, Service Bus 큐를 모니터링하여 메시지가 큐에 추가될 때마다 메일 알림을 보내도록 논리 앱을 설정합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기

1. **리소스 만들기** > **통합** > **Logic App** 을 선택합니다.

1. 다음 정보를 입력합니다.

   **이름**: 논리 앱의 이름입니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: IoT Hub에서 사용하는 것과 같은 위치를 사용합니다.

   ![Azure Portal에서 논리 앱 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. **만들기** 를 선택합니다.

### <a name="configure-the-logic-app-trigger"></a>논리 앱 트리거 구성

1. 논리 앱을 엽니다. 논리 앱으로 가져오는 가장 쉬운 방법은 리소스 창에서 **리소스 그룹** 을 선택하고 리소스 그룹을 선택한 다음, 리소스 목록에서 논리 앱을 선택하는 것입니다. 논리 앱을 선택하면 Logic Apps 디자이너가 열립니다.

1. Logic Apps 디자이너 창에서 **템플릿** 까지 스크롤하여 **비어 있는 논리 앱** 을 선택합니다.

   ![Azure Portal에서 빈 논리 앱 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. **모두** 탭을 선택한 다음, **Service Bus** 를 선택합니다.

   ![Service Bus를 선택하여 Azure Portal에서 논리 앱 만들기 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. **트리거** 에서 **큐에 하나 이상의 메시지가 도착하는 경우(자동 완성)** 을 선택합니다.

   ![Azure Portal에서 논리 앱에 대한 트리거 선택](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Service Bus 연결을 만듭니다.
   1. 연결 이름을 입력하고 목록에서 Service Bus 네임스페이스를 선택합니다. 다음 화면이 열립니다.

      ![큐에 하나 이상의 메시지가 도착하는 경우(자동 완성) 옵션이 강조 표시된 스크린샷](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Service Bus 정책(RootManageSharedAccessKey)을 선택합니다. **만들기** 를 선택합니다.

      ![Azure Portal에서 논리 앱에 대한 Service Bus 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 마지막 화면에서 **큐 이름** 으로 이전에 만든 큐를 드롭다운에서 선택합니다. **최대 메시지 수** 로 `175`을 입력합니다.

      ![논리 앱에서 Service Bus 연결의 최대 메시지 수 지정](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Logic Apps 디자이너의 맨 위에 있는 메뉴에서 **저장** 을 선택하여 변경 내용을 저장합니다.

### <a name="configure-the-logic-app-action"></a>논리 앱 작업 구성

1. SMTP 서비스 연결을 만듭니다.

   1. **새 단계** 를 선택합니다. **작업 선택** 에서 **모두** 탭을 선택합니다.

   1. 검색 상자에 `smtp`를 입력하고 검색 결과에서 **SMTP** 서비스를 선택한 다음, **메일 보내기** 를 선택합니다.

      ![Azure Portal에서 논리 앱에 SMTP 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. 사서함의 SMTP 정보를 입력한 다음 **만들기** 를 선택합니다.

      ![Azure Portal에서 논리 앱에 SMTP 연결 정보 입력](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) 및 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html)에 대한 SMTP 정보를 가져옵니다.

      > [!NOTE]
      > 연결을 설정하려면 TLS/SSL을 사용하지 않도록 설정해야 할 수도 있습니다. 이 경우 연결이 설정된 후 TLS를 다시 사용하도록 설정하려면 이 섹션의 끝에서 선택적으로 수행 가능한 단계를 참조하세요.

   1. **메일 보내기** 단계의 **새 매개 변수 추가** 드롭다운에서 **보낸 사람**, **받는** 사람, **제목**, **본문** 을 선택합니다. 화면에서 아무 곳이나 클릭하거나 탭하여 선택 상자를 닫습니다.

      ![SMTP 연결 메일 필드 선택](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. **보내는 사람** 및 **받는 사람** 의 전자 메일 주소를 입력하고, **제목** 및 **본문** 에 `High temperature detected`를 입력합니다. **이 흐름에서 사용된 앱 및 커넥터의 동적 콘텐츠 추가** 대화 상자가 열리면 **숨기기** 를 선택하여 닫습니다. 이 자습서에서는 동적 콘텐츠를 사용하지 않습니다.

      ![SMTP 연결 메일 필드 입력](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. **저장** 을 선택하여 SMTP 연결을 저장합니다.

1. (선택 사항) 메일 공급자와의 연결을 설정하기 위해 TLS를 사용하지 않도록 설정했는데 다시 사용하도록 설정하려는 경우 다음 단계를 수행하세요.

   1. **논리 앱** 창의 **개발 도구** 아래에서 **API 연결** 을 선택합니다.

   1. API 연결 목록에서 SMTP 연결을 선택합니다.

   1. **smtp API 연결** 창의 **일반** 에서 **API 연결 편집** 을 선택합니다.

   1. **API 연결 편집** 창에서 **SSL 사용?** 을 선택하고 메일 계정의 암호를 다시 입력한 다음, **저장** 을 선택합니다.

      ![Azure Portal에서 논리 앱의 SMTP API 연결 편집](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

이제 논리 앱이 Service Bus 큐의 온도 경고를 처리하고 메일 계정으로 알림을 보낼 준비가 되었습니다.

## <a name="test-the-logic-app"></a>논리 앱 테스트

1. 디바이스에서 클라이언트 애플리케이션을 시작합니다.

1. 물리적 디바이스를 사용하는 경우 열 센서 근처로 열원을 조심스럽게 가져와 온도가 30도를 초과할 때까지 둡니다. 온라인 시뮬레이터를 사용하는 경우 클라이언트 코드는 30도를 초과한다는 원격 분석 메시지를 임의로 출력합니다.

1. 논리 앱에서 보낸 메일 알림을 수신하기 시작해야 합니다.

   > [!NOTE]
   > 전자 메일을 보낸 사람이 본인이라는 것을 확인하기 위해 전자 메일 서비스 공급자가 발신자의 신분을 확인해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 성공적으로 만들었습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]