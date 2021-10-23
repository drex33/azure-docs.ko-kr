---
title: 앱을 다른 지역으로 이동
description: App Service 리소스를 한 지역에서 다른 지역으로 이동하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 67a3b0c8f5fd6a78ef65bdffe5f201ae42f90efe
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216678"
---
# <a name="move-an-app-service-resource-to-another-region"></a>App Service 리소스를 다른 지역으로 이동

이 문서에서는 App Service 리소스를 다른 Azure 지역으로 이동하는 방법을 설명합니다. 리소스를 다른 지역으로 이동하는 이유는 여러 가지가 있을 수 있습니다. 예를 들어 새 Azure 지역을 활용하거나, 특정 지역에서만 사용할 수 있는 기능이나 서비스를 배포하거나, 내부 정책 및 거버넌스 요구 사항을 충족하거나, 용량 계획 요구 사항에 대응하기 위해 리소스를 이동할 수 있습니다.

App Service 리소스는 지역에 따라 달라지므로 지역 간에 이동할 수 없습니다. 대상 지역에 기존 App Service 리소스의 복사본을 만든 다음 콘텐츠를 새 앱으로 이동해야 합니다. 원본 앱에서 사용자 지정 도메인을 사용하는 경우 이동이 완료되면 [대상 지역의 새 앱으로 해당 도메인을 마이그레이션](manage-custom-dns-migrate-domain.md)할 수 있습니다.

앱을 더욱 쉽게 복사하기 위해 다른 지역의 App Service 플랜으로 [개별 App Service 앱을 복제](app-service-web-app-cloning.md)할 수 있지만 이런 경우 [제한 사항](app-service-web-app-cloning.md#current-restrictions)이 있으며 특히 Linux 앱은 지원하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 이동하려는 Azure 지역에 App Service 앱이 있는지 확인합니다.
- 리소스를 이동할 App Service 및 관련 서비스를 대상 지역에서 지원하는지 확인합니다.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>준비

현재 사용 중인 모든 App Service 리소스를 식별합니다. 예를 들면 다음과 같습니다.

- App Service 앱
- [App Service 계획](overview-hosting-plans.md)
- [배포 슬롯](deploy-staging-slots.md)
- [Azure에서 구매한 사용자 지정 도메인](manage-custom-dns-buy-domain.md)
- [TLS/SSL 인증서](configure-ssl-certificate.md)
- [Azure Virtual Network 통합](./overview-vnet-integration.md)
- [하이브리드 연결](app-service-hybrid-connections.md)
- [관리 ID](overview-managed-identity.md)
- [백업 설정](manage-backup.md)

가져온 인증서 또는 하이브리드 연결과 같은 특정 리소스에는 다른 Azure 서비스와의 통합이 포함되어 있습니다. 해당 리소스를 지역 간에 이동하는 방법에 대한 자세한 내용은 각 서비스 설명서를 참조하세요.

## <a name="move"></a>이동

1. [원본 앱의 백업을 만듭니다](manage-backup.md).
1. [대상 지역에서 새 App Service 플랜의 앱을 만듭니다](app-service-plan-manage.md#create-an-app-service-plan).
2. [대상 앱에서 백업을 복원](web-sites-restore.md)합니다.
2. 사용자 지정 도메인을 사용하는 경우 먼저 `awverify.`를 사용하여 [대상 앱에 사용자 지정 도메인을 바인딩](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively)하고 [대상 앱에서 해당 도메인을 사용하도록 설정](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)합니다.
3. 대상 앱의 다른 모든 항목을 원본 앱과 동일하게 구성하고 구성을 확인합니다.
4. 사용자 지정 도메인이 대상 앱을 가리키도록 할 준비가 되면 [도메인 이름을 다시 매핑](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)합니다.

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>원본 리소스 정리

원본 앱 및 App Service 플랜을 삭제하세요. [무료가 아닌 계층의 App Service 플랜에는 실행 중인 앱이 없는 경우에도 요금이 부과됩니다.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>다음 단계

[PowerShell을 사용하여 Azure App Service 앱 복제](app-service-web-app-cloning.md)