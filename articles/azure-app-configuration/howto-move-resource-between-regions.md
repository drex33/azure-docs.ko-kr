---
title: App Configuration 저장소를 다른 지역으로 이동
description: App Configuration 저장소를 다른 지역으로 이동하는 방법을 알아봅니다.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: c3ffe773e16eb7a658f219e980711eca2add1aac
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123077523"
---
# <a name="move-an-app-configuration-store-to-another-region"></a>App Configuration 저장소를 다른 지역으로 이동 

App Configuration 저장소는 지역에 따라 다르기 대문에 자동으로 지역 간에 이동할 수 없습니다. 대상 지역에 새 App Configuration 저장소를 만든 다음, 원본 저장소에서 새 대상 저장소로 콘텐츠를 이동해야 합니다. 여러 가지 이유로 구성을 다른 지역으로 이동시킬 수도 있습니다. 예를 들어, 가용성 영역이 지원되는 새 Azure 지역을 이용하거나, 특정 지역에서만 사용할 수 있는 기능이나 서비스를 배포하거나, 내부 정책 및 거버넌스를 충족하기 위해서 구성을 이동할 수 있습니다. 

다음 단계에서는 새 대상 저장소를 만들고 현재 저장소를 새 지역으로 내보내는 프로세스를 안내합니다. 

## <a name="design-considerations"></a>디자인 고려 사항

시작하기 전에 다음과 같은 개념을 염두에 두어야 합니다.

* 구성 저장소 이름은 전역적으로 고유합니다. 
* 새 구성 저장소에서 액세스 정책 및 네트워크 구성 설정을 다시 구성해야 합니다.

## <a name="create-the-target-configuration-store"></a>대상 구성 저장소 만들기

### <a name="portal"></a>[포털](#tab/portal)     
포털에서 새 App Configuration 저장소를 만들려면, 다음 단계를 수행합니다. 
1.  [Azure Portal](https://portal.azure.com)에 로그인합니다. 홈 페이지의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다. **Marketplace 검색** 상자에서 *App Configuration* 을 입력하고 <kbd>Enter</kbd> 키를 선택합니다. 

    ![App Configuration 검색](../../includes/media/azure-app-configuration-create/azure-portal-search.png)
1. 검색 결과에서 **App Configuration**, **만들기** 를 차례로 선택합니다.

    ![만들기 선택](../../includes/media/azure-app-configuration-create/azure-portal-app-configuration-create.png)
1. **App Configuration 만들기** 창에서 다음 설정을 입력합니다.
    
    | 설정 | 제안 값 | Description |
    |---|---|---|
    | **구독** | 사용자의 구독 | 기존 저장소의 Azure 구독 선택 |
    | **리소스 그룹** | 리소스 그룹 | 기존 저장소의 Azure 리소스 그룹 선택 |
    | **리소스 이름** | 전역적으로 고유한 이름 | 대상 App Configuration 저장소에 사용할 고유한 리소스 이름을 입력합니다. 이 이름은 이전 구성 저장소와 같을 수 없습니다. |
    | **위치** | 대상 위치 | 구성 저장소를 이동할 대상 지역을 선택합니다. |
    | **가격 책정 계층** | *표준* | 원하는 가격 책정 계층을 선택합니다. 자세한 내용은 [App Configuration 가격 페이지](https://azure.microsoft.com/pricing/details/app-configuration)를 참조하세요. |
1. **검토 + 만들기** 를 선택하여 설정의 유효성을 검사합니다.
1. **만들기** 를 선택합니다. 배포에 몇 분 정도 걸릴 수 있습니다.
1. 리소스가 배포되면, 원본 저장소의 액세스 정책 및 네트워크 구성 설정을 다시 생성합니다. 이는 구성과 함께 전송되지 않습니다. 여기에는 ID, 가상 네트워크 및 공용 네트워크 액세스 관리를 사용하는 것이 포함될 수 있습니다. 
    
#### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
CLI에서 새 App Configuration 저장소를 만들려면 다음 단계를 수행합니다. 
1. 자격 증명을 사용하여 Azure CLI에 로그인합니다.
    ```azurecli
    az login
    ```
1. `create` 명령을 사용하여 새 구성 저장소를 생성합니다.
    ```azurecli
    az appconfig create -g MyResourceGroup -n MyResourceName -l targetlocation --sku Standard 
    ```
    다음 설정을 입력합니다.

    | 설정 | 제안 값 | Description |
    |---|---|---|
    | **리소스 그룹** | 리소스 그룹 | 기존 저장소의 Azure 리소스 그룹 선택 |
    | **리소스 이름** | 전역적으로 고유한 이름 | 대상 App Configuration 저장소에 사용할 고유한 리소스 이름을 입력합니다. 이 이름은 이전 구성 저장소와 같을 수 없습니다. |
    | **위치** | 대상 위치 | 구성 저장소를 이동할 대상 지역을 선택합니다. |
    | **Sku** | *Standard* | 원하는 가격 책정 계층을 선택합니다. 자세한 내용은 [App Configuration 가격 페이지](https://azure.microsoft.com/pricing/details/app-configuration)를 참조하세요. |
1. 배포에 몇 분 정도 걸릴 수 있습니다. 완료되면 원본 저장소의 액세스 정책 및 네트워크 구성 설정을 다시 생성합니다. 이는 구성 값과 함께 전송되지 않습니다. 여기에는 ID, 가상 네트워크 및 공용 네트워크 액세스 관리를 사용하는 것이 포함될 수 있습니다. 자세한 내용은 [CLI 설명서](./cli-samples.md)를 참조하세요.
---

## <a name="transfer-your-configuration-key-values"></a>구성 키-값 전송  

### <a name="portal"></a>[포털](#tab/portal)
포털을 사용하여 구성을 대상 저장소로 내보내려면 다음 단계를 수행합니다.
1. [Azure 포털](https://portal.azure.com)의 원본 구성 저장소로 이동하여 **작업** 아래에서 **가져오기/내보내기** 를 선택합니다.
1. **내보내기** 를 선택하고 **대상 서비스** 드롭다운에서 **App Configuration** 을 선택합니다. 
    ![다른 구성 저장소로 내보내기](media/export-to-config-store.png)
1. **리소스 선택** 을 클릭하고 **구독** 및 **리소스 그룹** 을 입력합니다. **리소스** 는 이전에 만든 대상 구성 저장소의 이름입니다. 
1. **적용** 을 선택하여 대상 구성 저장소를 확인합니다. 
1. 레이블, 시간 및 레이블 필드를 기본값으로 두고 **적용** 을 선택합니다. 
1. 구성이 원본에서 대상 저장소로 성공적으로 전송되었는지 확인하려면, 포털에서 대상 구성 저장소로 이동합니다. **작업** 에서 **구성 탐색기** 를 선택하고, 원래 저장소의 키 값 쌍과 동일한 키 값 쌍이 포함되어 있는지 확인합니다. 
    > [!NOTE]
    > 이 프로세스에서는 구성 키 값을 한 번에 하나의 레이블로 내보낼 수 있습니다. 여러 레이블을 내보내려면, 각 레이블에 대해 2-5단계를 반복합니다. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
다음 단계에 따라 Azure를 사용하여 구성을 대상 저장소로 내보냅니다.
1. Azure CLI에서 원본 구성 저장소의 모든 값을 대상 구성 저장소로 내보내는 다음 명령을 입력합니다. 
    ```azurecli
    az appconfig kv export -n SourceConfigurationStore -d appconfig --dest-name TargetConfigurationStore --key * --label * --preserve-labels
    ```
1. 구성이 원본에서 대상 저장소로 성공적으로 전송되었는지 확인하려면, 대상 저장소의 모든 키 값을 나열합니다. 
    ```azurecli
    az appconfig kv list -n TargetAppConfiguration --all
    ```
---
## <a name="delete-your-source-configuration-store"></a>원본 구성 저장소 삭제 

구성이 대상 저장소로 전송된 경우, 원본 구성 저장소를 삭제하도록 선택할 수 있습니다. 

### <a name="portal"></a>[포털](#tab/portal)
포털에서 원본 구성 저장소를 삭제하려면 다음 단계를 수행합니다.
1. [Azure Portal](https://portal.azure.com)에 로그인하고 **리소스 그룹** 을 선택합니다.
1. **이름으로 필터링** 상자에서 리소스 그룹의 이름을 입력합니다. 
1. 결과 목록에서 리소스 그룹 이름을 선택하여 개요를 확인합니다.
1. 원본 구성 저장소를 선택하고 **개요** 블레이드에서 **삭제** 를 선택합니다. 
1. 구성 저장소 삭제를 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

잠시 후 원본 구성 저장소가 삭제됩니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azcli)
다음 단계에 따라 Azure CLI 원본 구성 저장소를 삭제합니다.
1. Azure CLI에서 다음 명령을 실행합니다. 
    ```azurecli
    az appconfig delete -g ResourceGroupName -n SourceConfiguration
    ```
    **리소스 그룹** 은 원본 구성 저장소와 연결된 그룹입니다. 
1. 원본 구성 저장소를 삭제하는 데 몇 분 정도 걸릴 수 있습니다. 리소스 그룹의 현재 구성 저장소를 모두 나열하여 작업이 성공했는지 확인할 수 있습니다. 
    ```azurecli
    az appconfig list -g MyResourceGroup
    ```
    잠시 후 원본 구성 저장소가 삭제됩니다.

---
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure App Configuration 저장소](./howto-move-resource-between-regions.md)
>[Azure App Configuration 복원력 및 재해 복구](./concept-disaster-recovery.md)에서 자동으로 키-값을 백업합니다
