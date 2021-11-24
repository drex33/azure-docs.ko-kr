---
title: Azure Notification Hubs에서 푸시 알림 설정 | Microsoft Docs
description: PNS(플랫폼 알림 시스템) 설정을 사용하여 Azure Portal에 Azure Notification Hubs를 설정하는 방법을 알아봅니다.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 08/23/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.custom: devx-track-azurecli, mode-other
ms.openlocfilehash: ba48ff2493341dbaccb4ea65ab939f6b27d0c80b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133047968"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>빠른 시작: 알림 허브에서 푸시 알림 설정

Azure Notification Hubs는 사용하기 쉽고 확장 가능한 푸시 엔진을 제공합니다. Notification Hubs를 사용하여 모든 플랫폼(iOS, Android, Windows, Baidu) 및 백 엔드(클라우드 또는 온-프레미스)에 알림을 보냅니다. 자세한 내용은 [Azure Notification Hubs란?](notification-hubs-push-notification-overview.md)를 참조하세요.

이 빠른 시작에서는 Notification Hubs의 PNS(플랫폼 알림 시스템) 설정을 사용하여 여러 플랫폼에서 푸시 알림을 설정합니다. 빠른 시작은 Azure Portal에서 수행할 단계를 보여줍니다. [Google Firebase Cloud Messaging](?tabs=azure-cli#google-firebase-cloud-messaging-fcm)에는 Azure CLI 사용 지침이 포함되어 있습니다.

알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에서 Azure 알림 허브 만들기](create-notification-hub-portal.md) 또는 [Azure CLI를 사용하여 Azure 알림 허브 만들기](create-notification-hub-azure-cli.md)를 참조하세요.

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

APNS(Apple Push Notification Service)를 설정하려면 다음을 수행합니다.

1. Azure Portal의 **알림 허브** 페이지 왼쪽 메뉴에서 **Apple(APNS)** 을 선택합니다.

1. **인증 모드** 의 경우 **인증서** 또는 **토큰** 을 선택합니다.

   a. **인증서** 를 선택한 경우:
   * 파일 아이콘을 선택한 다음, 업로드할 *.p12* 파일을 선택합니다.
   * 암호를 입력합니다.
   * **샌드박스** 모드를 선택합니다. 또는 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려면 **프로덕션** 모드를 선택합니다.

     ![Azure Portal의 APNS 인증서 구성 스크린샷](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. **토큰** 을 선택한 경우:

   * **키 ID**, **번들 ID**, **팀 ID** 및 **토큰** 에 대한 값을 입력합니다.
   * **샌드박스** 모드를 선택합니다. 또는 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려면 **프로덕션** 모드를 선택합니다.

     ![Azure Portal의 APNS 토큰 구성 스크린샷](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

자세한 내용은 [Azure Notification Hubs를 사용하여 iOS 앱에 푸시 알림 보내기](ios-sdk-get-started.md)를 참조하세요.

## <a name="google-firebase-cloud-messaging-fcm"></a>Google FCM(Firebase Cloud Messaging)

# <a name="portal"></a>[포털](#tab/azure-portal)

Google FCM용 푸시 알림을 설정하려면 다음을 수행합니다.

1. Azure Portal의 **알림 허브** 페이지 왼쪽 메뉴에서 **Google(GCM/FCM)** 을 선택합니다.
2. 앞에서 저장한 Google FCM 프로젝트의 **API 키** 를 붙여넣습니다.
3. **저장** 을 선택합니다.

   ![Google FCM에 대한 Notification Hubs를 구성하는 방법을 보여주는 스크린샷](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

이 단계를 완료하면 알림 허브 업데이트가 성공적으로 업데이트되었음을 나타내는 경고가 표시됩니다. **Save** 단추가 비활성화됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Google FCM(Firebase Cloud Messaging) 프로젝트용 **API 키** 가 필요합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- 이 문서에는 Azure CLI 버전 2.0.67 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

### <a name="set-up-push-notifications-for-google-fcm"></a>Google FCM용 푸시 알림 설정

1. [az notification-hub credential gcm update](/cli/azure/notification-hub/credential/gcm#az_notification_hub_credential_gcm_update) 명령을 사용하여 Google API 키를 알림 허브에 추가합니다.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Android 앱에는 알림 허브에 연결하기 위한 연결 문자열이 필요합니다.  [az notification-hub authorization-rule list](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list) 명령을 사용하여 사용 가능한 액세스 정책을 나열합니다.  [az notification-hub authorization-rule list-keys](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list_keys) 명령을 사용하여 액세스 정책 연결 문자열을 가져옵니다.  기본 연결 문자열을 직접 가져오려면 `--query` 매개 변수에 **primaryConnectionString** 또는 **secondaryConnectionString** 을 지정합니다.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. [az notification-hub test-send](/cli/azure/notification-hub#az_notification_hub_test_send) 명령을 사용하여 Android 앱으로 메시지 보내기를 테스트합니다.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

[az notification-hub credential](/cli/azure/notification-hub/credential) 명령을 사용하여 다른 플랫폼에 대한 Azure CLI 참조를 가져옵니다.

Android 애플리케이션에 알림을 보내는 방법에 대한 자세한 내용은 [Firebase를 사용하여 Android 디바이스에 푸시 알림 보내기](notification-hubs-android-push-notification-google-fcm-get-started.md)를 참조하세요.

---

## <a name="windows-push-notification-service"></a>Windows Push Notification Service

WNS(Windows Push Notification Service)를 설정하려면 다음을 수행합니다.

1. Azure Portal의 **알림 허브** 페이지 왼쪽 메뉴에서 **Windows(WNS)** 를 선택합니다.
2. **패키지 SID** 및 **보안 키** 에 사용할 값을 입력합니다.
3. **저장** 을 선택합니다.

   ![패키지 SID 및 보안 키 상자를 보여주는 스크린샷](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

자세한 내용은 [Azure Notification Hubs를 사용하여 UWP에 알림 보내기](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)를 참조하세요.

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Windows Phone용 Microsoft Push Notification Service

> [!NOTE]
> MPNS(Microsoft 푸시 알림 서비스)는 더 이상 사용되지 않으며 지원하지 않습니다.

Windows Phone용 MPNS(Microsoft Push Notification Service)를 설정하려면 다음을 수행합니다.

1. Azure Portal의 **알림 허브** 페이지 왼쪽 메뉴에서 **Windows Phone(MPNS)** 을 선택합니다.
1. 인증되지 않았거나 인증된 푸시 알림 사용:

   a. 인증되지 않은 푸시 알림을 사용하도록 설정하려면 **인증되지 않은 푸시 사용** > **저장** 을 선택합니다.

      ![인증되지 않은 푸시 알림을 사용하도록 설정하는 방법을 보여주는 스크린샷](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. 인증된 푸시 알림을 사용하도록 설정하려면 다음을 수행합니다.
      * 도구 모음에서 **인증서 업로드** 를 선택합니다.
      * 파일 아이콘을 선택한 다음, 인증서 파일을 선택합니다.
      * 인증서에 대한 암호를 입력합니다.
      * **확인** 을 선택합니다.
      * **Windows Phone(MPNS)** 페이지에서 **저장** 을 선택합니다.

자세한 내용은 [Notification Hubs를 사용하여 Windows Phone 앱에 푸시 알림](notification-hubs-windows-mobile-push-notifications-mpns.md)을 참조하세요.

## <a name="baidu-android-china"></a>Baidu(Android China)

Baidu용 푸시 알림을 설정하려면 다음을 수행합니다.

1. Azure Portal의 **알림 허브** 페이지 왼쪽 메뉴에서 **Baidu(Android China)** 를 선택합니다.
2. Baidu 클라우드 푸시 프로젝트의 Baidu 콘솔에서 가져온 **API 키** 를 입력합니다.
3. Baidu 클라우드 푸시 프로젝트의 Baidu 콘솔에서 가져온 **비밀 키** 를 입력합니다.
4. **저장** 을 선택합니다.

    ![푸시 알림에 대한 Baidu(Android China) 구성을 보여주는 Notification Hubs의 스크린샷](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

이 단계를 완료하면 알림 허브 업데이트가 성공적으로 업데이트되었음을 나타내는 경고가 표시됩니다. **Save** 단추가 비활성화됩니다.

자세한 내용은 [Baidu를 사용하여 Notification Hubs 시작](notification-hubs-baidu-china-android-notifications-get-started.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal의 알림 허브에 대한 플랫폼 알림 시스템을 구성하는 방법을 알아봤습니다.

알림을 다양한 플랫폼으로 푸시하는 방법에 대한 자세한 내용은 다음 자습서를 참조하세요.

* [Azure Notification Hubs를 사용하여 iOS 앱에 푸시 알림 보내기](ios-sdk-get-started.md)
* [Notification Hubs 및 Google FCM을 사용하여 Android 디바이스에 알림 보내기](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Windows 디바이스에서 실행 중인 UWP 앱에 알림 보내기](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [MPNS를 사용하여 Windows Phone 8 앱에 알림 보내기](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Notification Hubs 및 Baidu 클라우드 푸시를 사용하여 알림 보내기](notification-hubs-baidu-china-android-notifications-get-started.md)
