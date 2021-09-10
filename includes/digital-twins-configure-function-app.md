---
author: baanders
description: Azure Digital Twins에서 작동하도록 Azure 함수를 구성하는 방법을 설명하는 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 6aabec311df33c1d08d12b48117d9763ccf1761d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803263"
---
Azure CLI 또는 Azure Portal을 사용하여 함수 앱에 대한 보안 액세스를 설정할 수 있습니다. 원하는 옵션에 따라 단계를 수행합니다.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [digital-twins-configure-function-app-cli.md](digital-twins-configure-function-app-cli.md)]

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[Azure Portal](https://portal.azure.com/)에서 다음 단계를 완료합니다.

#### <a name="assign-an-access-role"></a>액세스 역할 할당

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

시스템 할당 관리 ID를 사용하면 코드에 자격 증명을 저장하지 않고도 Azure 리소스가 클라우드 서비스(예: Azure Key Vault)에서 인증받을 수 있습니다. 시스템 할당 관리 ID를 사용하도록 설정한 후에는 Azure 역할 기반 액세스 제어를 통해 필요한 모든 권한을 부여할 수 있습니다. 

이 형식의 관리 ID 수명 주기는 이 리소스의 수명 주기와 연결되어 있습니다. 또한 각 리소스는 하나의 시스템 할당 관리 ID만 가질 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에서 검색 상자에 해당 이름을 입력하여 함수 앱을 검색합니다. 결과에서 앱을 선택합니다. 

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Azure Portal 스크린샷. 포털 검색 창에 함수 앱의 이름이 입력되어 있으며, 검색 결과가 강조 표시됩니다.":::

1. 함수 앱 페이지 왼쪽 메뉴에서 __ID__ 를 선택하여 함수에 대한 관리 ID를 사용합니다. __시스템 할당__ 페이지에서 __상태__ 가 **켜짐** 으로 설정되어 있는지 확인합니다. 그렇지 않은 경우 지금 설정한 다음, 변경 내용을 **저장** 합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png" alt-text="Azure Portal의 스크린샷. 함수 앱에 대한 ID 페이지에서 상태 옵션이 켜짐으로 설정되어 있습니다." lightbox="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png":::

1. __Azure 역할 할당__ 을 선택합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png" alt-text="Azure Portal 스크린샷. Azure Function의 ID 페이지에서 권한 아래에 Azure 역할 할당 단추가 강조 표시됩니다." lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png":::

    __+ 역할 할당 추가(미리 보기)__ 를 선택합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png" alt-text="Azure Portal 스크린샷. Azure 역할 할당 페이지에서 역할 할당 추가(미리 보기) 단추가 강조 표시됩니다." lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png":::

1. __역할 할당 추가(미리 보기)__ 페이지에서 다음 값을 선택합니다.

    * **범위**: _리소스 그룹_
    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: 리소스 그룹을 선택합니다.
    * **역할**: _Azure Digital Twins 데이터 소유자_

    __저장__ 을 선택하여 세부 정보를 저장합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-3.png" alt-text="새 역할 할당 추가 방법을 보여주는 Azure Portal의 스크린샷. 대화 상자에는 범위, 구독, 리소스 그룹 및 역할에 대한 필드가 표시됩니다.":::

#### <a name="configure-application-settings"></a>애플리케이션 설정 구성

Azure Digital Twins 인스턴스의 URL에서 함수에 액세스할 수 있도록 환경 변수를 설정할 수 있습니다. 애플리케이션 설정은 환경 변수로 표시되어 Azure Digital Twins 인스턴스에 대한 액세스를 허용합니다. 환경 변수에 대한 자세한 내용은 [함수 앱 관리](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)를 참조하세요. 

인스턴스의 URL을 사용하여 환경 변수를 설정하려면 먼저 인스턴스의 호스트 이름을 확인합니다. 

1. [Azure Portal](https://portal.azure.com)에서 인스턴스를 검색합니다. 
1. 왼쪽 메뉴에서 __개요__ 를 선택합니다. 
1. __호스트 이름__ 을 복사합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/instance-host-name.png" alt-text="Azure Portal의 스크린샷. 인스턴스의 개요 페이지에서 호스트 이름 값이 강조 표시됩니다.":::

이제 애플리케이션 설정을 만들 수 있습니다.

1. 포털 검색 창에서 함수 앱을 검색한 다음, 결과에서 앱을 선택합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Azure Portal 스크린샷. 포털 검색 창에서 함수 앱의 이름을 검색 중입니다. 검색 결과가 강조 표시됩니다.":::

1. 왼쪽에서 __구성__ 을 선택합니다. 그런 다음, __애플리케이션 설정__ 탭에서 __+ 새 애플리케이션 설정__ 을 선택합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting.png" alt-text="Azure Portal의 스크린샷. 함수 앱에 대한 구성 탭에서 새 애플리케이션 설정을 만드는 단추가 강조 표시됩니다.":::

1. 창이 열리면 복사해둔 호스트 이름 값을 사용하여 애플리케이션 설정을 만듭니다.
    * **이름**: `ADT_SERVICE_URL`
    * **값**: `https://<your-Azure-Digital-Twins-host-name>`
    
    __확인__ 을 선택하여 애플리케이션 설정을 만듭니다.
    
    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-application-setting.png" alt-text="Azure Portal의 스크린샷. 애플리케이션 설정 추가/편집 페이지에 이름 및 값 필드가 입력되어 있습니다. 확인 단추가 강조 표시됩니다.":::

1. 설정을 만들면 __애플리케이션 설정__ 탭에 나타납니다. **ADT_SERVICE_URL** 이 목록에 표시되는지 확인합니다. 그런 다음, __저장__ 을 선택하여 새 애플리케이션 설정을 저장합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting-save-details.png" alt-text="Azure Portal의 스크린샷. 애플리케이션 설정 탭에서 새 ADT SERVICE URL 설정과 저장 단추가 모두 강조 표시됩니다.":::

1. 애플리케이션 설정을 변경하면 애플리케이션을 다시 시작해야 하므로 메시지가 표시되면 __계속__ 을 선택하여 애플리케이션을 다시 시작합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/save-application-setting.png" alt-text="Azure Portal의 스크린샷. 애플리케이션 설정을 변경하면 애플리케이션이 다시 시작된다는 메모가 표시됩니다.":::

---
