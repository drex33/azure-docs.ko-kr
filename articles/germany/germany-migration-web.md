---
title: Azure 독일에서 글로벌 Azure로 Azure 웹 리소스 마이그레이션
description: 이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure 웹 리소스를 마이그레이션하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: d67a4aadbe22b5447aa73063e48c0ca43fb45d04
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122566177"
---
# <a name="migrate-web-resources-to-global-azure"></a>글로벌 Azure로 웹 리소스 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure 웹 리소스를 마이그레이션하는 데 도움이 되는 정보를 제공합니다.

## <a name="web-apps"></a>Web Apps

지금은 Azure App Service의 Web Apps 기능을 사용하여 만든 앱을 Azure 독일에서 글로벌 Azure로 마이그레이션할 수 없습니다. 웹앱을 Azure Resource Manager 템플릿으로 내보내는 것이 좋습니다. 그런 다음, 위치 속성을 새 대상 지역으로 변경한 후 다시 배포합니다.

> [!IMPORTANT]
> 새 지역과 일치하도록 위치, Azure Key Vault 비밀, 인증서 및 기타 GUID를 변경합니다.

### <a name="migrate-web-app-resource"></a>웹앱 리소스 마이그레이션

1. Azure 독일 구독에서 [웹앱 및 App Service 요금제를 템플릿으로 내보냅니다](../azure-resource-manager/templates/export-template-portal.md). 웹앱 리소스 그룹에서 마이그레이션할 리소스를 선택하고 템플릿으로 내보냅니다.
1. 템플릿을 zip 파일로 다운로드합니다. 
1. **template.json** 파일에서 위치 속성을 대상 Azure 글로벌 지역으로 편집합니다. 예를 들어 다음 JSON 파일에서 대상 위치는 ‘미국 서부’입니다.

    ```json
        "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2018-02-01",
            "name": "[parameters('serverfarms_myappservice_name')]",
            "location": "West US",

    ```
1. 수정된 템플릿을 Azure 글로벌에 배포합니다. 예를 들어 PowerShell을 사용하여 배포할 수 있습니다.

    ```powershell
    az deployment group create --name "<web app name>" \
        --resource-group "<resource group name>" \
        --template-file "<path of your template.json file>"
    ```

### <a name="migrate-web-app-content"></a>웹앱 콘텐츠 마이그레이션

1. Azure 독일 포털에서 웹앱을 선택합니다.
1. **개발 도구 > 고급 도구** 를 선택합니다.
1. 상단 메뉴에서 **디버그 콘솔**, **PowerShell** 을 차례로 선택합니다.
1. **사이트** 를 선택합니다.
1. **wwwroot** 폴더 옆에 있는 **다운로드 아이콘** 을 선택합니다. 다운로드한 zip 파일에는 웹앱의 원본 코드가 포함되어 있습니다.
1. 마이그레이션된 Azure 글로벌 웹앱에 웹 루트를 배포합니다. 예를 들어 다음 PowerShell 스크립트를 사용할 수 있습니다.

    ``` powershell
    az webapp deployment source config-zip \
        --resource-group "<resource group name>" \
        --name "<web App name>" \
        --src "path to webroot folder zip file"
    ```

추가 정보는 다음 항목을 참조하세요.

- [App Service 자습서](../app-service/tutorial-dotnetcore-sqldb-app.md)를 완료하여 정보를 새로 고치세요.
- [Azure Resource Manager 템플릿을 내보내는](../azure-resource-manager/templates/export-template-portal.md) 방법에 대한 정보를 확인하세요.
- [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)를 검토하세요.
- [App Service 개요](../app-service/overview.md)를 검토하세요.
- [Azure 위치 개요](https://azure.microsoft.com/global-infrastructure/locations/)를 확인하세요.
- [템플릿을 다시 배포](../azure-resource-manager/templates/deploy-powershell.md)하는 방법을 알아보세요.

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs 인스턴스 간에 설정을 마이그레이션하려면 태그를 사용하여 모든 등록 토큰을 내보내고 가져옵니다.

1. Azure Blob Storage 컨테이너로 [기존 알림 허브 등록을 내보냅니다](/previous-versions/azure/azure-services/dn790624(v=azure.100)).
1. 대상 환경에서 새 알림 허브를 만듭니다.
1. Blob Storage에서 새 알림 허브로 [등록 토큰을 가져옵니다](/previous-versions/azure/azure-services/dn790624(v=azure.100)).

추가 정보는 다음 항목을 참조하세요.

- [Notification Hubs 자습서](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)를 완료하여 정보를 새로 고치세요.
- [Notification Hubs 개요](../notification-hubs/notification-hubs-push-notification-overview.md)를 검토하세요.

## <a name="event-hubs"></a>Event Hubs

Azure Event Hub를 마이그레이션하려면 Azure 독일에서 Event Hub 리소스 템플릿을 내보낸 다음, 글로벌 Azure에 템플릿을 배포합니다.

1. Azure 독일 구독에서 [Event Hub를 템플릿으로 내보냅니다](../azure-resource-manager/templates/export-template-portal.md).
1. 글로벌 Azure 구독에 [사용자 지정 템플릿으로 Event Hub 템플릿을 배포](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)합니다. Azure 독일 구독에서 내보낸 템플릿을 로드하고 배포합니다.

추가 정보는 다음 항목을 참조하세요.

- [Event Hubs 개요](../event-hubs/event-hubs-about.md)를 검토하세요.
- [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)를 검토하세요.
- [Azure Resource Manager 템플릿을 내보내는](../azure-resource-manager/templates/export-template-portal.md) 방법에 대한 정보를 확인하세요.
- [템플릿을 다시 배포](../azure-resource-manager/templates/deploy-powershell.md)하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스를 마이그레이션하기 위한 도구, 기술, 권장 사항에 대해 알아보세요.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)