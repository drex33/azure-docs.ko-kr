---
title: Azure Notification Hub를 통해 사용자에게 플랫폼 중립적 알림 보내기(ASP.NET)
description: Notification Hubs 템플릿을 사용하여 모든 플랫폼을 대상으로 하는 플랫폼 중립적인 알림을 단일 요청으로 보내는 방법을 보여 줍니다.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 08/23/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 01d6d1cffaa0ae9928c24015570f882036b1e783
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771439"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Azure Notification Hubs를 사용하여 플랫폼 간 알림 보내기

이 자습서는 이전 자습서 [Azure Notification Hubs를 사용하여 특정 사용자에게 알림 보내기]를 기반으로 합니다. 이전 자습서에서는 인증된 특정 사용자에게 등록된 모든 디바이스에 알림을 푸시하는 방법을 설명합니다. 해당 접근 방식에는 지원되는 각 클라이언트 플랫폼에 알림을 보내기 위해 여러 요청이 필요했습니다. Azure Notification Hubs는 특정 디바이스가 알림을 받는 방법을 지정할 수 있는 템플릿을 지원합니다. 이 경우 플랫폼 중립적 알림 전송이 간소화됩니다.

이 문서에서는 템플릿을 활용하여 모든 플랫폼을 대상으로 하는 알림을 보내는 방법을 보여 줍니다. 이 문서에서는 단일 요청을 사용하여 플랫폼 중립적 알림을 보냅니다. 템플릿에 대한 자세한 내용은 [Azure Notification Hubs 개요][Templates]를 참조하세요.

> [!IMPORTANT]
> Windows Phone 프로젝트 8.1 및 이전 버전은 Visual Studio 2019에서 지원되지 않습니다. 자세한 내용은 [Visual Studio 2019 플랫폼 대상 지정 및 호환성](/visualstudio/releases/2019/compatibility)을 참조하세요.

> [!NOTE]
> Notification Hubs를 사용하면 디바이스가 동일한 태그로 여러 템플릿을 등록할 수 있습니다. 이 경우 해당 태그를 대상으로 들어오는 메시지가 있으면 각 템플릿에 대해 하나씩 여러 개의 알림이 디바이스에 전달됩니다. 이 프로세스로 Windows 스토어 앱에 알림 메시지와 배지 둘 다로 표시하는 등 여러 시각적 알림에 동일한 메시지를 표시할 수 있습니다.

## <a name="send-cross-platform-notifications-using-templates"></a>템플릿을 사용하여 플랫폼 간 알림 보내기

> [!NOTE]
> MPNS(Microsoft 푸시 알림 서비스)는 사용되지 않으며 더 이상 지원되지 않습니다.

이 섹션에서는 [Azure Notification Hubs를 사용하여 특정 사용자에게 알림 보내기] 자습서에서 빌드한 샘플 코드를 사용합니다. [전체 샘플은 GitHub에서 다운로드](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers)할 수 있습니다.

템플릿을 사용하여 플랫폼 중립적 알림을 보내려면 다음을 수행합니다.

1. Visual Studio의 **솔루션 탐색기** 에서 **컨트롤러** 폴더를 확장한 다음 *RegisterController.cs* 파일을 엽니다.

1. `Put` 메서드에서 새 등록을 만드는 코드 블록을 찾은 다음, `switch`의 내용을 다음 코드로 바꿉니다.

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    이 코드는 플랫폼 특정 메서드를 호출하여 기본 등록이 아니라 템플릿 등록을 만듭니다. 템플릿 등록은 기본 등록에서 파생되므로 기존 등록을 수정할 필요는 없습니다.

1. **솔루션 탐색기** 에서 **컨트롤러** 폴더를 확장하고 **NotificationsController.cs** 파일을 엽니다. `Post` 메서드를 다음 코드로 바꿉니다.

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    이 코드는 동시에 모든 플랫폼으로 알림을 보냅니다. 기본 페이로드를 지정하지 않습니다. Notification Hubs는 등록된 템플릿에 지정된 대로 올바른 페이로드를 작성하고 제공된 tag 값을 가진 모든 디바이스에 전달합니다.

1. Web API 프로젝트를 다시 게시합니다.

1. 클라이언트 앱을 다시 실행하여 등록에 성공했는지 확인합니다.

1. 경우에 따라 클라이언트 앱을 두 번째 디바이스에 배포한 후 앱을 실행합니다. 각 디바이스에 알림이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서를 마쳤습니다. 이제 다음 문서에서 Notification Hubs 및 템플릿에 대해 자세히 알아보세요.

* 템플릿 사용에 대한 다른 시나리오는 [유니버설 Windows 플랫폼 애플리케이션을 실행하는 특정 Windows 디바이스에 알림 푸시][Use Notification Hubs to send breaking news] 자습서를 참조하세요.
* 템플릿에 대한 자세한 내용은 [Notification Hubs 개요][Templates]를 참조하세요.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Azure Notification Hubs를 사용하여 특정 사용자에게 알림 보내기]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)