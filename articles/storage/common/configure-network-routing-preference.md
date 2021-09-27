---
title: 네트워크 라우팅 기본 설정 구성
titleSuffix: Azure Storage
description: Azure Storage 계정의 네트워크 라우팅 기본 설정을 구성하여 네트워크 트래픽이 인터넷을 통해 클라이언트에서 계정으로 라우팅되는 방식을 지정합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c871a1ec4feec89cc3250f1fbfefefa69ed927bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589324"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Azure Storage의 네트워크 라우팅 기본 설정 구성

이 문서에서는 스토리지 계정에 대한 네트워크 라우팅 기본 설정 및 경로별 엔드포인트를 구성하는 방법을 설명합니다.

네트워크 라우팅 기본 설정에서 네트워크 트래픽이 인터넷을 통해 클라이언트에서 계정으로 라우팅되는 방식을 지정합니다. 경로별 엔드포인트는 Azure Storage에서 스토리지 계정에 대해 만드는 새 엔드포인트입니다. 이러한 엔드포인트는 기본 라우팅 기본 설정을 변경하지 않고 원하는 경로를 통해 트래픽을 라우팅합니다. 자세히 알아보려면 [Azure Storage의 네트워크 라우팅 기본 설정](network-routing-preference.md)을 참조하세요.

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>기본 퍼블릭 엔드포인트에 대한 라우팅 기본 설정 구성

기본적으로 스토리지 계정의 퍼블릭 엔드포인트에 대한 라우팅 기본 설정은 Microsoft 글로벌 네트워크로 설정됩니다. 스토리지 계정의 퍼블릭 엔드포인트에 대한 기본 라우팅 기본 설정으로 Microsoft 글로벌 네트워크 또는 인터넷 라우팅을 선택할 수 있습니다. 이러한 두 라우팅 유형 간의 차이점에 대한 자세한 내용은 [Azure Storage의 네트워크 라우팅 기본 설정](network-routing-preference.md)을 참조하세요.

### <a name="portal"></a>[포털](#tab/azure-portal)

라우팅 기본 설정을 인터넷 라우팅으로 변경하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Portal에서 스토리지 계정으로 이동합니다.

3. **설정** 아래에서 **네트워킹** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![네트워킹 메뉴 옵션](./media/configure-network-routing-preference/networking-option.png)

4. **방화벽 및 가상 네트워크** 탭의 **네트워크 라우팅** 에서 **라우팅 기본 설정** 을 **인터넷 라우팅** 으로 변경합니다.

5. **저장** 을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![인터넷 라우팅 옵션](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. `Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

   ```powershell
   Connect-AzAccount
   ```

2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. 라우팅 기본 설정을 인터넷 라우팅으로 변경하려면 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 사용하고 `--routing-choice` 매개 변수를 `InternetRouting`으로 설정합니다.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   `<resource-group-name>` 자리 표시자 값을 스토리지 계정을 포함하는 리소스 그룹의 이름으로 바꿉니다.

   `<storage-account-name>` 자리 표시자 값을 스토리지 계정의 이름으로 바꿉니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure 구독에 로그인합니다.

   - Azure Cloud Shell을 시작하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다.

   - CLI의 로컬 설치로 로그인하려면 [az login](/cli/azure/reference-index#az_login) 명령을 실행합니다.

     ```azurecli
     az login
     ```

2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. 라우팅 기본 설정을 인터넷 라우팅으로 변경하려면 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 명령을 사용하고 `--routing-choice` 매개 변수를 `InternetRouting`으로 설정합니다.

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

---

## <a name="configure-a-route-specific-endpoint"></a>경로별 엔드포인트 구성

경로별 엔드포인트를 구성할 수도 있습니다. 예를 들어, 기본 엔드포인트에 대한 라우팅 기본 설정을 *인터넷 라우팅* 으로 설정한 다음, 인터넷의 클라이언트와 스토리지 계정 간의 트래픽을 Microsoft 글로벌 네트워크를 통해 라우팅할 수 있도록 하는 경로별 엔드포인트를 게시할 수 있습니다.

이 기본 설정은 경로별 엔드포인트에만 영향을 줍니다. 이 기본 설정은 기본 라우팅 기본 설정에는 영향을 주지 않습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. Portal에서 스토리지 계정으로 이동합니다.

2. **설정** 아래에서 **네트워킹** 을 선택합니다.

3. **방화벽 및 가상 네트워크** 탭의 **경로별 엔드포인트 게시** 에서 경로별 엔드포인트의 라우팅 기본 설정을 선택하고 **저장** 을 클릭합니다.

    다음 그림은 선택된 **Microsoft 네트워크 라우팅** 옵션을 보여 줍니다.

    > [!div class="mx-imgBorder"]
    > ![Microsoft 네트워크 라우팅 옵션](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 경로별 엔드포인트를 구성하려면 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 사용합니다.

   - Microsoft 네트워크 라우팅 기본 설정을 사용하는 경로별 엔드포인트를 만들려면 `-PublishMicrosoftEndpoint` 매개 변수를 `true`로 설정합니다.

   - 인터넷 라우팅 기본 설정을 사용하는 경로별 엔드포인트를 만들려면 `-PublishInternetEndpointTo` 매개 변수를 `true`로 설정합니다.

   다음 예제에서는 Microsoft 네트워크 라우팅 기본 설정을 사용하는 경로별 엔드포인트를 만듭니다.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   `<resource-group-name>` 자리 표시자 값을 스토리지 계정을 포함하는 리소스 그룹의 이름으로 바꿉니다.

   `<storage-account-name>` 자리 표시자 값을 스토리지 계정의 이름으로 바꿉니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 경로별 엔드포인트를 구성하려면 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 명령을 사용합니다.

   - Microsoft 네트워크 라우팅 기본 설정을 사용하는 경로별 엔드포인트를 만들려면 `--publish-microsoft-endpoints` 매개 변수를 `true`로 설정합니다.

   - 인터넷 라우팅 기본 설정을 사용하는 경로별 엔드포인트를 만들려면 `--publish-internet-endpoints` 매개 변수를 `true`로 설정합니다.

   다음 예제에서는 Microsoft 네트워크 라우팅 기본 설정을 사용하는 경로별 엔드포인트를 만듭니다.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   `<storage-account-name>` 자리 표시자 값을 스토리지 계정의 이름으로 바꿉니다.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>경로별 엔드포인트의 엔드포인트 이름 찾기

경로별 엔드포인트를 구성한 경우 스토리지 계정의 속성에서 엔드포인트를 찾을 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. **설정** 아래에서 **속성** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![속성 메뉴 옵션](./media/configure-network-routing-preference/properties.png)

2. 라우팅 기본 설정을 지 원하는 각 서비스에 대해 **Microsoft 네트워크 라우팅** 엔드포인트가 표시됩니다. 이 이미지는 Blob 및 파일 서비스에 대한 엔드포인트를 보여 줍니다.

    > [!div class="mx-imgBorder"]
    > ![경로별 엔드포인트에 대한 Microsoft 네트워크 라우팅 옵션](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 엔드포인트를 콘솔에 출력하려면 스토리지 계정 개체의 `PrimaryEndpoints` 속성을 사용합니다.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   `<resource-group-name>` 자리 표시자 값을 스토리지 계정을 포함하는 리소스 그룹의 이름으로 바꿉니다.

   `<storage-account-name>` 자리 표시자 값을 스토리지 계정의 이름으로 바꿉니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 엔드포인트를 콘솔에 출력하려면 스토리지 계정 개체의 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 속성을 사용합니다.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   `<resource-group-name>` 자리 표시자 값을 스토리지 계정을 포함하는 리소스 그룹의 이름으로 바꿉니다.

   `<storage-account-name>` 자리 표시자 값을 스토리지 계정의 이름으로 바꿉니다.

---

## <a name="see-also"></a>참고 항목

- [네트워크 라우팅 기본 설정](network-routing-preference.md)
- [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md)
- [Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
