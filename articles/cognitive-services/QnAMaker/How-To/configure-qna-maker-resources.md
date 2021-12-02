---
title: QnA Maker 서비스 설정 - QnA Maker
description: 이 문서에서는 QnA Maker 리소스에 대한 고급 구성을 간략하게 설명합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/25/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: e45b2c135634eaf5c674479e77353bb05ef5cab1
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436649"
---
# <a name="configure-qna-maker-resources"></a>QnA Maker 리소스 구성

사용자는 다른 Cognitive Search 리소스를 사용하도록 QnA Maker를 구성할 수 있습니다. QnA Maker GA를 사용하는 경우 App Service 설정을 구성할 수도 있습니다.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>다른 Cognitive Search 리소스를 사용하도록 QnA Maker 구성

> [!NOTE]
> QnA Maker와 연결된 Azure Search Service를 변경하면 이미 있는 모든 기술 자료에 대한 액세스 권한이 손실됩니다. Azure Search Service를 변경하기 전에 기존 기술 자료를 내보내야 합니다.

포털을 통해 QnA 서비스 및 해당 종속성(예: Search)을 만드는 경우 Search Service가 만들어지고 QnA Maker 서비스에 연결됩니다. 이러한 리소스가 만들어지면 이전에 기존 Search Service를 사용하고 방금 만든 서비스를 제거하도록 App Service 설정을 업데이트할 수 있습니다.

QnA Maker의 **App Service** 리소스는 Cognitive Search 리소스를 사용합니다. QnA Maker에서 사용하는 Cognitive Search 리소스를 변경하려면 Azure Portal에서 설정을 변경해야 합니다.

1. QnA Maker가 사용할 Cognitive Search 리소스의 **관리자 키** 와 **이름** 을 가져옵니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 QnA Maker 리소스와 연결된 **App Service** 를 찾습니다. 둘 다 동일한 이름을 가지고 있습니다.

1. **설정**, **구성** 을 차례로 선택합니다. 그러면 QnA Maker의 App Service에 대한 모든 기존 설정이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![App Service 구성 설정을 보여 주는 Azure Portal의 스크린샷](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 다음 키에 대한 값을 변경합니다.

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 새 설정을 사용하려면 App Service를 다시 시작해야 합니다. **개요** 를 선택한 다음, **다시 시작** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![구성 설정이 변경된 후 App Service를 다시 시작하는 Azure Portal 스크린샷](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Azure Resource Manager 템플릿을 통해 QnA 서비스를 만드는 경우, 모든 리소스를 만들고 기존 Search Service를 사용하는 App Service 만들기를 제어할 수 있습니다.

App Service [애플리케이션 설정](../../../app-service/configure-common.md#configure-app-settings)을 구성하는 방법에 대해 자세히 알아보세요.

## <a name="get-the-latest-runtime-updates"></a>최신 런타임 업데이트 받기

QnAMaker 런타임은 Azure Portal에서 [QnAMaker 서비스를 만들](./set-up-qnamaker-service-azure.md) 때 배포한 Azure App Service 인스턴스의 일부입니다. 런타임은 주기적으로 업데이트됩니다. QnA Maker App Service 인스턴스는 2019년 4월 사이트 확장 릴리스(버전 5 이상) 이후 자동 업데이트 모드에 있습니다. 이 업데이트는 업그레이드하는 동안 가동 중지 시간을 0으로 처리하도록 설계되었습니다.

https://www.qnamaker.ai/UserSettings 에서 현재 버전을 확인할 수 있습니다. 귀하의 버전이 버전 5.x 보다 이전 버전인 경우 App Service를 다시 시작하여 최신 업데이트를 적용해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. App Service 인스턴스를 선택하고 **개요** 섹션 열기

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service 인스턴스](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. App Service를 다시 시작합니다. 업데이트 프로세스는 몇 초 내에 완료되어야 합니다. 이 QnAMaker 서비스를 사용하는 모든 종속 애플리케이션 또는 봇은 이 재시작 기간 동안 최종 사용자가 사용할 수 없습니다.

    ![QnAMaker App Service 인스턴스 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>시간 초과를 방지하도록 App Service 유휴 설정 구성

게시된 기술 자료에 대한 QnA Maker 예측 런타임을 제공하는 App Service에는 서비스가 유휴 상태이면 자동으로 시간이 초과되도록 기본적으로 설정되는 유휴 시간 제한 구성이 있습니다. QnA Maker의 경우, 이는 트래픽이 없는 기간 후에도 예측 런타임 generateAnswer API가 때때로 시간 초과되는 것을 의미합니다.

트래픽이 없는 경우에도 예측 엔드포인트 앱이 로드되도록 하려면 유휴 상태를 Always On으로 설정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. QnA Maker 리소스의 App Service를 검색하고 선택합니다. QnA Maker 리소스와 동일한 이름을 갖지만 다른 **유형** 의 App Service를 갖게 됩니다.
1. **설정** 을 찾은 다음, **구성** 을 선택합니다.
1. 구성 창에서 **일반 설정** 을 선택하고 **Always On** 을 찾은 다음, 값으로 **On** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![구성 창에서 **일반 설정**을 선택한 다음, **Always On**을 찾고 값으로 **On**을 선택합니다.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. **저장** 을 선택하여 구성을 저장합니다.
1. 새 설정을 사용하도록 앱을 다시 시작할지 묻는 메시지가 표시됩니다. **계속** 을 선택합니다.

App Service [일반 설정](../../../app-service/configure-common.md#configure-general-settings)을 구성하는 방법에 대해 자세히 알아보세요.

## <a name="business-continuity-with-traffic-manager"></a>Traffic Manager를 통한 비즈니스 연속성

비즈니스 연속성 계획의 주요 목표는 Bot 또는 이를 사용하는 애플리케이션에 대한 작동 중지 시간이 없도록 보장하는 복원력 있는 기술 자료 엔드포인트를 만드는 것입니다.

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 계획](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

위에 표시된 것처럼 대략적인 아이디어는 다음과 같습니다.

1. [Azure 쌍을 이루는 지역](../../../availability-zones/cross-region-replication-azure.md)에서 두 개의 병렬 [QnA Maker 서비스](set-up-qnamaker-service-azure.md)를 설정합니다.

1. 기본 QnA Maker App Service를 [백업](../../../app-service/manage-backup.md)하고 보조 설정에서 [복원](../../../app-service/web-sites-restore.md)합니다. 이렇게 하면 두 설정이 동일한 호스트 이름 및 키로 작동합니다.

1. 기본 및 보조 Azure Search 인덱스를 동기화된 상태로 유지합니다. Azure 인덱스를 백업-복원하는 방법을 보려면 [여기](https://github.com/pchoudhari/QnAMakerBackupRestore)에서 GitHub 샘플을 사용하세요.

1. [연속 내보내기](../../../azure-monitor/app/export-telemetry.md)를 사용하여 Application Insights를 백업합니다.

1. 기본 및 보조 스택이 설치되면 [트래픽 관리자](../../../traffic-manager/traffic-manager-overview.md)를 사용하여 두 개의 엔드포인트를 구성하고 라우팅 메서드를 설정합니다.

1. Traffic Manager 엔드포인트에 대해 이전에는 SSL(Secure Sockets Layer)로 알려진 TLS(전송 계층 보안) 인증서를 만들어야 합니다. App Service에서 [TLS/SSL 인증서를 바인딩](../../../app-service/configure-ssl-bindings.md)합니다.

1. 마지막으로, 봇 또는 앱에서 트래픽 관리자 엔드포인트를 사용합니다.
