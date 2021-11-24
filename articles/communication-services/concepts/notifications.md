---
title: Azure Communication Services의 알림
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services를 기반으로 하는 앱의 사용자에게 알림을 보냅니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: ffe88d8aaa33c22c6fa9ba2cd6db5dd60389e116
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132938080"
---
# <a name="communication-services-notifications"></a>Communication Services 알림

Azure Communication Services 채팅 및 통화 SDK는 신호 메시지를 효율적이고 안정적인 방식으로 연결된 클라이언트에 푸시할 수 있는 실시간 메시징 채널을 만듭니다. 이를 통해 복잡한 HTTP 폴링 로직을 구현하지 않고도 다양한 실시간 통신 기능을 애플리케이션에 구축할 수 있습니다. 하지만 모바일 애플리케이션에서 이 신호 채널은 애플리케이션이 포그라운드에서 활성 상태인 경우에만 연결된 상태를 유지합니다. 애플리케이션이 백그라운드에 있는 동안 걸려오는 전화나 채팅 메시지를 사용자가 받도록 하려면 푸시 알림을 사용해야 합니다.

푸시 알림을 사용하면 애플리케이션에서 사용자의 모바일 디바이스로 정보를 보낼 수 있습니다. 푸시 알림을 사용하여 대화 상자를 표시하거나 소리를 재생하거나 걸려오는 전화 UI를 표시할 수 있습니다. Azure Communication Services는 앱에 푸시 알림을 추가하는 데 사용할 수 있는 [Azure Event Grid](../../event-grid/overview.md) 및 [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md)와의 통합을 제공합니다.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Azure Event Grid를 통해 푸시 알림 트리거

Azure Communication Services는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합되어 안정적이고 확장 가능하며 안전한 방식으로 실시간 이벤트 알림을 제공합니다. 이 통합을 활용하여 [Azure 함수](../../azure-functions/functions-overview.md) 또는 웹후크를 트리거하는 이벤트 그리드 구독을 만들어서 사용자에게 모바일 푸시 알림을 제공하는 알림 서비스를 만들 수 있습니다.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Communication Services가 Event Grid와 통합되는 방식을 보여주는 다이어그램":::

[Azure Communication Services에서 이벤트 처리](../../event-grid/event-schema-communication-services.md)에 대해 자세히 알아보세요.

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Azure Notification Hubs를 통해 푸시 알림 제공

Azure Notification Hub를 Communication Services 리소스에 연결하여 걸려오는 전화를 받을 때 사용자의 모바일 디바이스에 푸시 알림을 자동으로 보내거나 누락된 채팅 활동에 대해 알릴 수 있습니다. 이러한 푸시 알림을 사용하여 백그라운드에서 애플리케이션을 깨우고 사용자가 통화를 수락 또는 거부하거나 새로 받은 채팅 메시지를 읽을 수 있는 UI를 표시해야 합니다. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Communication Services가 Azure Notification Hubs와 통합되는 방식을 보여 주는 다이어그램":::

Communication Services는 Azure Notification Hub를 통과 서비스로 사용하여 [Direct Send](/rest/api/notificationhubs/direct-send) API를 통해 다양한 플랫폼별 푸시 알림 서비스와 통신합니다. 이렇게 하면 기존 Azure Notification Hub 리소스 및 구성을 다시 사용하여 대기 시간이 짧고 안정적인 알림을 애플리케이션에 전달할 수 있습니다.

> [!NOTE]
> 현재 호출 푸시 알림은 Android 및 iOS에서 모두 지원됩니다. 채팅 푸시 알림은 버전 1.1.0-beta.4의 Android SDK에 대해서만 지원됩니다.

### <a name="notification-hub-provisioning"></a>Notification Hub 프로비저닝

Notification Hubs를 사용하여 클라이언트 디바이스에 푸시 알림을 전달하려면 Communication Services 리소스와 동일한 구독 내에서 [Notification Hub를 만듭니다](../../notification-hubs/create-notification-hub-portal.md). Azure Notification Hub는 사용할 플랫폼 알림 시스템에 대해 구성되어야 합니다. Notification Hubs에서 클라이언트 앱의 푸시 알림을 받는 방법을 알아보려면 [Notification Hubs 시작](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)을 참조하고 페이지 위쪽의 드롭다운 목록에서 대상 클라이언트 플랫폼을 선택하세요.

> [!NOTE]
> 현재 APN 및 FCM 플랫폼이 지원됩니다.
APN 플랫폼은 토큰 인증 모드로 구성되어야 합니다. 현재 인증서 인증 모드는 지원되지 않습니다.

알림 허브가 구성되면 Azure Resource Manager 클라이언트를 사용하여 또는 Azure Portal을 통해 허브에 대한 연결 문자열을 제공하여 Communication Services 리소스에 연결할 수 있습니다. 연결 문자열에는 `Send` 권한이 포함되어야 합니다. 허브 전용으로 `Send` 권한이 있는 액세스 정책을 따로 만드는 것이 좋습니다. [Notification Hubs 보안 및 액세스 정책](../../notification-hubs/notification-hubs-push-notification-security.md)에 대한 자세한 정보

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Azure Resource Manager 클라이언트를 사용하여 Notification Hub 연결

Azure Resource Manager에 로그인하려면 다음을 실행하고 자격 증명을 사용하여 로그인합니다.

```console
armclient login
```

 로그인이 성공적으로 완료되면 다음을 실행하여 알림 허브를 프로비저닝합니다.

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Azure Portal을 사용하여 Notification Hub 연결

1. 포털에서 Azure Communication Services 리소스로 이동합니다.

1. Communication Services 리소스 내에서 Communication Services 페이지의 왼쪽 메뉴에 있는 **푸시 알림** 을 선택하고 이전에 프로비저닝한 Notification Hub를 연결합니다.

1. **알림 허브 연결** 을 선택합니다. 연결할 수 있는 알림 허브의 목록이 표시됩니다.
 
1. 이 리소스에 사용할 알림 허브를 선택합니다.
 
   - 새 허브를 만들어야 하는 경우 **새 알림 허브 만들기** 를 선택하여 이 리소스에 대해 프로비저닝된 새 허브를 가져옵니다.

   :::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Azure Portal 내의 푸시 알림 설정을 보여 주는 스크린샷":::

이제 연결된 상태와 연결된 알림 허브가 표시됩니다.

리소스에 대해 다른 허브를 사용하려는 경우 **연결 끊기** 를 선택한 다음, 단계를 반복하여 다른 알림 허브를 연결합니다.

> [!NOTE]
> 허브를 연결하는 방법에 대한 변경 내용은 최대 10분 내에 데이터 평면(즉, 알림을 보낼 때)에 반영됩니다. 변경 전에 알림이 전송된 **경우** 허브를 처음 연결하는 경우에도 이와 동일한 동작이 적용됩니다.

### <a name="device-registration"></a>디바이스 등록

Communication Services에 디바이스 핸들을 등록하는 방법을 알아보려면 [음성 통화 빠른 시작](../quickstarts/voice-video-calling/getting-started-with-calling.md) 및 [채팅 빠른 시작](../quickstarts/chat/get-started.md)을 참조하세요.

### <a name="troubleshooting-guide-for-push-notifications"></a>푸시 알림 문제 해결 가이드

디바이스에 푸시 알림이 표시되지 않는다면 세 위치에서 알림이 삭제된 것일 수 있습니다.

- Azure Notification Hubs가 Azure Communication Services의 알림을 수락하지 않음
- 플랫폼 알림 시스템(예: APN 및 FCM)이 Azure Notification Hubs의 알림을 수락하지 않음
- 플랫폼 알림 시스템이 디바이스에 알림을 배달하지 않았습니다.

알림이 삭제될 수 있는 첫 번째 위치(Azure Notification Hubs가 Azure Communication Services의 알림을 수락하지 않음)는 아래에 설명되어 있습니다. 다른 두 위치는 [Azure Notification Hubs의 삭제된 알림 진단](../../notification-hubs/notification-hubs-push-notification-fixer.md)을 참조하세요.

Communication Services 리소스가 Azure Notification Hubs에 알림을 전송하는지 확인하는 한 가지 방법은 연결된 [Azure Notification Hub 메트릭](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs)에서 `incoming messages` 메트릭을 살펴보는 것입니다.

다음은 Azure Notification Hub가 Communication Services 리소스의 알림을 수락하지 않는 원인이 될 수 있는 몇 가지 일반적인 잘못된 구성을 설명합니다.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Communication Services 리소스에 연결되지 않은 Azure Notification Hub

Azure Notification Hub를 Communication Services 리소스에 연결하지 않은 경우가 있을 수 있습니다. [Notification Hub 프로비저닝 섹션](#notification-hub-provisioning)에서 연결 방법을 확인할 수 있습니다.

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>연결된 Azure Notification Hub가 구성되지 않음

사용하려는 플랫폼(예: iOS 또는 android)에 대한 플랫폼 알림 시스템 자격 증명을 사용하여 연결된 Notification Hub를 구성해야 합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [알림 허브에 푸시 알림 설정](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)을 참조하세요.

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>연결된 Azure Notification Hub가 없음

Communication Services 리소스에 연결된 Azure Notification Hub가 더 이상 존재하지 않습니다. 연결된 Notification Hub가 여전히 존재하는지 확인합니다.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>Azure Notification Hub APN 플랫폼이 인증서 인증 모드로 구성됨

APN 플랫폼을 인증서 인증 모드로 사용하는 기능은 현재 지원되지 않습니다. [알림 허브에 푸시 알림 설정](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)에 나와 있는 것처럼 토큰 인증 모드로 APN 플랫폼을 구성해야 합니다.

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>연결된 연결 문자열에 `Send` 권한이 없음

Notification Hub를 Communication Services 리소스에 연결하는 데 사용한 연결 문자열에는 `Send` 권한이 있어야 합니다. 새 연결 문자열을 만들거나 Azure Notification Hub에서 현재 연결 문자열을 확인하는 방법에 대한 자세한 내용은 [Notification Hubs 보안 및 액세스 정책](../../notification-hubs/notification-hubs-push-notification-security.md)을 참조하세요.

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>연결된 연결 문자열 또는 Azure Notification Hub 리소스 ID가 유효하지 않음

올바른 연결 문자열 및 Azure Notification Hub 리소스 ID를 사용하여 Communication Services 리소스를 구성해야 합니다.

#### <a name="the-linked-connection-string-is-regenerated"></a>연결된 연결 문자열이 다시 생성됨

연결된 Azure Notification Hub의 연결 문자열을 다시 생성한 경우 [Notification Hub를 다시 연결](#notification-hub-provisioning)하여 Communication Services 리소스의 새 연결 문자열로 연결 문자열을 업데이트해야 합니다.

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](../../event-grid/overview.md)을 참조하세요.
* Azure Notification Hub 개념에 대해 자세히 알아보려면 [Azure Notification Hubs 설명서](../../notification-hubs/index.yml)를 참조하세요.
