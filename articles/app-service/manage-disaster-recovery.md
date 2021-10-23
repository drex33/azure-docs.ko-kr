---
title: 지역 전체의 오류에서 복구
description: Azure App Service가 BCDR(비즈니스 연속성 및 재해 복구) 기능을 유지하는 데 도움이 되는 방법을 알아봅니다. Azure에서 특정 지역 전체의 오류로부터 앱을 복구합니다.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 418c5077a685e8f5289644f4652638e605b6d456
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240474"
---
# <a name="move-an-app-service-app-to-another-region"></a>App Service 앱을 다른 지역으로 이동

이 문서에서는 특정 Azure 지역 전체에 영향을 주는 재해가 발생하는 경우 다른 Azure 지역에서 App Service 리소스를 다시 온라인 상태로 전환하는 방법을 설명합니다. 재해로 인해 특정 Azure 지역 전체가 오프라인 상태가 되면 해당 지역에 호스트된 모든 App Service 앱은 재해 복구 모드가 됩니다. 앱을 다른 지역으로 복원하거나 영향을 받는 앱에서 파일을 복구하는 데 도움이 되는 기능을 사용할 수 있습니다.

App Service 리소스는 지역에 따라 달라지므로 지역 간에 이동할 수 없습니다. 앱을 다른 지역의 새 앱으로 복원한 다음, 새 앱에 대한 미러링 구성 또는 리소스를 만들어야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 없음 [스냅샷에서 복원](app-service-web-restore-snapshots.md)은 일반적으로 **프리미엄** 계층을 사용해야 하지만, 재해 복구 모드에서는 영향을 받는 앱이 속한 계층에 관계없이 자동으로 영향을 받는 앱에 사용하도록 설정됩니다.

## <a name="prepare"></a>준비

영향을 받는 앱에서 현재 사용하는 모든 App Service 리소스를 식별합니다. 예를 들면 다음과 같습니다.

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

## <a name="restore-app-to-a-different-region"></a>다른 지역으로 앱 복원

1. 영향을 받는 앱이 속한 곳과 ‘다른’ Azure 지역에 새 App Service 앱을 만듭니다. 이 앱이 재해 복구 시나리오의 대상 앱입니다.

1. [Azure Portal](https://portal.azure.com)에서 영향을 받는 앱의 관리 페이지로 이동합니다. 오류가 발생한 Azure 지역에서는 영향을 받는 앱이 경고 텍스트를 표시합니다. 경고 텍스트를 클릭합니다.

    ![영향을 받는 앱 페이지 스크린샷. 상황을 설명하고 앱 복원 링크를 제공하는 경고 알림이 표시됩니다.](media/manage-disaster-recovery/restore-start.png)

1. **백업 복원** 페이지에서 다음 표에 따라 복원 작업을 구성합니다. 완료되었으면 **확인** 을 클릭합니다.

   | 설정 | 값 | 설명 |
   |-|-|-|
   | **스냅샷(미리 보기)** | 스냅샷 선택 | 두 개의 최근 스냅샷이 제공됩니다. |
   | **복원 대상** | **기존 앱** | 아래에서 **복원 대상 앱을 변경하려면 여기를 클릭** 이라고 표시된 메모를 클릭하고 대상 앱을 선택합니다. 재해 시나리오에서는 다른 Azure 지역에 있는 앱으로만 스냅샷을 복원할 수 있습니다. |
   | **사이트 구성 복원** | **예** | |

    ![백업 복원 페이지 스크린샷. 앞의 표에 나열된 옵션 및 확인 단추가 강조 표시된 특정 스냅샷입니다.](media/manage-disaster-recovery/restore-configure.png)

3. 대상 앱의 [다른 모든 항목](#prepare)을 구성하여 영향을 받는 앱을 미러링하고 구성을 확인합니다.

4. 사용자 지정 도메인이 대상 앱을 가리키도록 할 준비가 되면 [도메인 이름을 다시 매핑](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)합니다.

## <a name="recover-app-content-only"></a>앱 콘텐츠만 복구

영향을 받는 앱은 복원하지 않고 영향을 받는 앱의 파일만 복구하려는 경우 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에서 영향을 받는 앱의 관리 페이지로 이동하여 **게시 프로필 가져오기** 를 클릭합니다.

    ![영향을 받는 앱 페이지 스크린샷. 경고 알림이 표시되지만 강조 표시되지 않습니다. 대신, 게시 프로필 가져오기 항목이 강조 표시됩니다.](media/manage-disaster-recovery/get-publish-profile.png)

1. 다운로드한 파일을 열고 이름에 `ReadOnly - FTP`가 포함된 게시 프로필을 찾습니다. 이 프로필이 재해 복구 프로필입니다. 예를 들면 다음과 같습니다.

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    세 개의 특성 값을 복사합니다. 
        
    - `publishUrl`: FTP 호스트 이름
    - `userName` 및 `userPWD`: FTP 자격 증명

1. 원하는 FTP 클라이언트를 선택하고 이 호스트 이름 및 자격 증명을 사용하여 영향을 받는 앱의 FTP 호스트에 연결합니다.

1. 연결되면 전체 */site/wwwroot* 폴더를 다운로드합니다. 다음 스크린샷에서는 어떻게 [FileZilla](https://filezilla-project.org/)에서 다운로드했는지 보여 줍니다.

    ![FileZilla 파일 계층 구조 스크린샷. wwwroot 폴더가 강조 표시되고 폴더의 바로 가기 메뉴를 보여 줍니다. 해당 메뉴에서 다운로드가 강조 표시되어 있습니다.](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>다음 단계
[스냅샷에서 Azure의 앱 복원](app-service-web-restore-snapshots.md)