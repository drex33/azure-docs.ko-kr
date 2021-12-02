---
title: 로컬 공유로 Azure Storage 탑재(컨테이너)
description: Azure App Service의 컨테이너화된 앱에서 사용자 지정 네트워크 공유를 연결하는 방법을 알아봅니다. 앱 간에 파일을 공유하고, 정적 콘텐츠를 원격으로 관리하고, 로컬로 액세스하는 등의 작업을 수행합니다.
author: msangapu-msft
ms.topic: article
ms.date: 09/02/2021
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f67bc2bd3dbfc3e382bf825d0b86f12ef48ff140
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436668"
---
# <a name="mount-azure-storage-as-a-local-share-in-a-custom-container-in-app-service"></a>App Service 사용자 지정 컨테이너에 로컬 공유로 Azure Storage 탑재

::: zone pivot="container-windows"

> [!NOTE]
>App Service Windows 컨테이너의 Azure Storage는 현재 **미리 보기** 로 제공되며 **프로덕션 시나리오** 를 **지원하지 않습니다**.

이 가이드에서는 Azure Storage Files를 App Service의 Windows 컨테이너에 네트워크 공유로 탑재하는 방법을 보여줍니다. [Azure Files 공유](../storage/files/storage-how-to-use-files-portal.md)와 [프리미엄 파일 공유](../storage/files/storage-how-to-create-file-share.md)만 지원됩니다. 사용자 지정 탑재 스토리지의 이점은 다음과 같습니다.

::: zone-end

::: zone pivot="container-linux"

이 가이드에서는 Azure Storage를 App Service의 기본 제공 Linux 컨테이너 또는 사용자 지정 Linux 컨테이너에 네트워크 공유로 탑재하는 방법을 보여줍니다. [Azure Storage 로컬 공유로 탑재하는 방법을](https://www.youtube.com/watch?v=OJkvpWYr57Y)비디오를 참조하세요. 사용자 지정 탑재 스토리지의 이점은 다음과 같습니다.

::: zone-end

- App Service 앱에 대한 영구 스토리지를 구성하고 스토리지를 별도로 관리합니다.
- 비디오 및 이미지와 같은 정적 콘텐츠를 App Service 앱에 즉시 사용할 수 있습니다. 
- 애플리케이션 로그 파일을 쓰거나 오래된 애플리케이션 로그를 Azure 파일 공유에 보관합니다.  
- 여러 앱 간에 또는 다른 Azure 서비스와 콘텐츠를 공유합니다.

::: zone pivot="container-windows"

다음은 Windows 컨테이너에서 지원되는 기능입니다.

- [프라이빗 링크를](../storage/common/storage-private-endpoints.md) 사용하여 스토리지 계정에 대한 보안 [액세스(VNET 통합을](./overview-vnet-integration.md) 사용하는 경우) [서비스 엔드포인트](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) 지원은 현재 사용할 수 없습니다.
- Azure Files(읽기/쓰기)
- 앱당 최대 5개의 탑재 지점
- 드라이브 문자 할당( `C:` `Z:` ~).

::: zone-end

::: zone pivot="container-linux"

다음은 Linux 컨테이너에서 지원되는 기능입니다.

- [서비스 엔드포인트](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) 및 [프라이빗 링크](../storage/common/storage-private-endpoints.md)를 통해([VNET 통합](./overview-vnet-integration.md)을 사용하는 경우) 스토리지 계정에 안전하게 액세스
- Azure Files(읽기/쓰기)
- Azure Blob(읽기 전용)
- 앱당 최대 5개의 탑재 지점

::: zone-end

## <a name="prerequisites"></a>사전 요구 사항

::: zone pivot="container-windows"

- [Azure App Service 기존 Windows 사용자 지정 컨테이너](quickstart-custom-container.md)
- [Azure 파일 공유 만들기](../storage/files/storage-how-to-use-files-portal.md)
- [Azure 파일 공유에 파일 업로드](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- 기존 [App Service on Linux 앱](index.yml)
- [Azure Storage 계정](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Azure 파일 공유 및 디렉터리](../storage/files/storage-how-to-use-files-portal.md)

::: zone-end

> [!NOTE]
> Azure Storage는 기본이 아닌 App Service용 스토리지이며 요금이 App Service에 포함되지 않고 별도로 청구됩니다.
>

## <a name="limitations"></a>제한 사항

::: zone pivot="container-windows"

- 네이티브 Windows(컨테이너화되지 않은) 앱에는 스토리지 탑재가 지원되지 않습니다.
- Azure Blob은 지원되지 않습니다.
- [Storage 방화벽은](../storage/common/storage-network-security.md) [프라이빗 엔드포인트를](../storage/common/storage-private-endpoints.md) 통해서만 [지원됩니다(VNET 통합을](./overview-vnet-integration.md) 사용하는 경우). 현재는 탑재된 Azure Storage 계정에서 프라이빗 엔드포인트를 사용하는 경우에는 사용자 지정 DNS 지원을 사용할 수 없습니다.
- 탑재된 스토리지에 대한 FTP/FTPS 액세스는 지원되지 않습니다([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) 사용).
- , , 및 를 `[C-Z]:\` `[C-Z]:\home` 사용자 지정 탑재 `/` `/home` 스토리지에 매핑하는 것은 지원되지 않습니다.
- 스토리지 탑재는 [배포 슬롯](deploy-staging-slots.md)을 만드는 동안 복제 설정 옵션과 함께 사용할 수 없습니다.
- 스토리지 탑재는 [앱을 백업](manage-backup.md)할 때 백업되지 않습니다. 모범 사례에 따라 Azure Storage 계정을 백업해야 합니다. 

::: zone-end

::: zone pivot="container-linux"

- [스토리지 방화벽](../storage/common/storage-network-security.md)은 [서비스 엔드포인트](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) 및 [프라이빗 엔드포인트](../storage/common/storage-private-endpoints.md)를 통해서만 제공됩니다([VNET 통합](./overview-vnet-integration.md)을 사용하는 경우). 현재는 탑재된 Azure Storage 계정에서 프라이빗 엔드포인트를 사용하는 경우에는 사용자 지정 DNS 지원을 사용할 수 없습니다.
- 사용자 지정 탑재 스토리지에 대한 FTP/FTPS 액세스는 지원되지 않습니다([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) 사용).
- Azure CLI, Azure PowerShell 및 Azure SDK 지원은 미리 보기로 제공됩니다.
- `/` 또는 `/home`을 사용자 지정 탑재 스토리지에 매핑할 수 없습니다.
- 사용자 지정 스토리지 탑재를 `/tmp` 또는 해당 하위Directories에 매핑하지 마세요. 이로 인해 앱 시작 중에 시간 초과가 발생할 수 있습니다.
- 스토리지 탑재는 [배포 슬롯](deploy-staging-slots.md)을 만드는 동안 복제 설정 옵션과 함께 사용할 수 없습니다.
- 스토리지 탑재는 [앱을 백업](manage-backup.md)할 때 백업되지 않습니다. 모범 사례에 따라 Azure Storage 계정을 백업해야 합니다. 

::: zone-end

::: zone pivot="container-windows"
## <a name="mount-storage-to-windows-container"></a>Windows 컨테이너에 스토리지 탑재
::: zone-end
::: zone pivot="container-linux"
## <a name="mount-storage-to-linux-container"></a>Linux 컨테이너에 스토리지 탑재
::: zone-end

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에서 앱으로 이동합니다.
1. 왼쪽의 탐색 창에서 **구성** > **경로 매핑** > **새 Azure Storage 탑재** 를 클릭합니다. 
1. 다음 표에 따라 스토리지 탑재를 구성합니다. 완료되었으면 **확인** 을 클릭합니다.

    ::: zone pivot="container-windows"
    | 설정 | 설명 |
    |-|-|
    | **이름** | 탑재 구성의 이름입니다. 공백은 허용되지 않습니다. |
    | **구성 옵션** | 스토리지 계정이 [프라이빗 엔드포인트를](../storage/common/storage-private-endpoints.md)사용하지 않는 경우 **기본을** 선택합니다. 사용하고 있으면 **고급** 를 선택합니다. |
    | **스토리지 계정** | Azure Storage 계정. Azure Files 공유를 포함해야 합니다. |
    | **공유 이름** | 탑재할 파일을 공유합니다. |
    | **액세스 키**(고급만 해당) | 스토리지 계정의 [액세스 키](../storage/common/storage-account-keys-manage.md)입니다. |
    | **탑재 경로** | Azure Storage 탑재할 Windows 컨테이너 내의 디렉터리입니다. 루트 디렉터리( 또는 `[C-Z]:\` `/` ) 또는 `home` 디렉터리( , 또는 )를 사용하지 `[C-Z]:\home` `/home` 마십시오.|
    ::: zone-end
    ::: zone pivot="container-linux"
    | 설정 | 설명 |
    |-|-|
    | **이름** | 탑재 구성의 이름입니다. 공백은 허용되지 않습니다. |
    | **구성 옵션** | 스토리지 계정에서 [서비스 엔드포인트](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) 또는 [프라이빗 엔드포인트](../storage/common/storage-private-endpoints.md)를 사용하고 있지 않으면 **기본** 을 선택합니다. 사용하고 있으면 **고급** 를 선택합니다. |
    | **스토리지 계정** | Azure Storage 계정. |
    | **스토리지 유형** | 탑재하려는 스토리지에 맞는 유형을 선택합니다. Azure Blob은 읽기 전용 액세스만 지원합니다. |
    | **스토리지 컨테이너** 또는 **공유 이름** | 탑재할 파일 공유 또는 Blob 컨테이너입니다. |
    | **액세스 키**(고급만 해당) | 스토리지 계정의 [액세스 키](../storage/common/storage-account-keys-manage.md)입니다. |
    | **탑재 경로** | Azure Storage에 탑재할 Linux 컨테이너 내부의 디렉터리입니다. `/` 또는 `/home`을 사용하지 마세요.|
    ::: zone-end

    > [!CAUTION]
    > 컨테이너의 **탑재 경로에** 지정된 디렉터리를 비워 두어야 합니다. 이 디렉터리에 저장된 모든 콘텐츠는 Azure Storage가 탑재될 때 삭제됩니다(예: `/home` 아래에 있는 디렉터리를 지정하는 경우). 기존 앱에 대한 파일을 마이그레이션하는 경우 시작하기 전에 앱과 앱의 콘텐츠를 백업하세요.
    >
    
# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) 명령 사용 예를 들면 다음과 같습니다.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

::: zone pivot="container-windows"
- `--storage-type`은 Windows 컨테이너용 `AzureFiles`여야 합니다. 
- `mount-path-directory`는 드라이브 문자 없이 `/path/to/dir` 또는 `[C-Z]:\path\to\dir` 형식이어야 합니다. 루트 디렉터리 ( `[C-Z]:\` 또는 `/` ) 또는 `home` 디렉터리 (또는 `[C-Z]:\home` )를 사용 하지 마십시오 `/home` .
::: zone-end
::: zone pivot="container-linux"
- `--storage-type`은 `AzureBlob` 또는 `AzureFiles`가 될 수 있습니다. `AzureBlob`이(가) 읽기 전용입니다.
- `--mount-path`는 Azure Storage에 탑재할 Linux 컨테이너 내부의 디렉터리입니다. `/`(루트 디렉터리)를 사용하지 마세요.
::: zone-end

다음 명령을 실행하여 스토리지가 탑재되었는지 확인합니다.

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

> [!CAUTION]
> 컨테이너에 지정 된 디렉터리는 `--mount-path` 비어 있어야 합니다. 이 디렉터리에 저장된 모든 콘텐츠는 Azure Storage가 탑재될 때 삭제됩니다(예: `/home` 아래에 있는 디렉터리를 지정하는 경우). 기존 앱에 대한 파일을 마이그레이션하는 경우 시작하기 전에 앱과 앱의 콘텐츠를 백업하세요.
>

다음 명령을 실행하여 구성을 확인합니다.

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

---

> [!NOTE]
> 스토리지 탑재를 추가, 편집 또는 삭제하면 앱이 다시 시작됩니다. 

::: zone pivot="container-linux"

## <a name="test-the-mounted-storage"></a>탑재된 스토리지 테스트

앱에 대한 Azure Storage가 성공적으로 탑재되었는지 확인하려면 다음을 수행합니다.

1. 컨테이너에 대한 [SSH 세션을 엽니다](configure-linux-open-ssh-session.md).
1. SSH 터미널에서 다음 명령을 실행합니다.

    ```bash
    df –h 
    ```
1. 스토리지 공유가 탑재되었는지 확인합니다. 스토리지 공유가 없는 경우 스토리지 공유 탑재에 문제가 있는 것입니다.
1. 다음 명령을 실행하여 스토리지 탑재의 대기 시간 또는 일반적인 도달 가능성을 확인합니다.

    ```bash
    tcpping Storageaccount.file.core.windows.net 
    ```

::: zone-end

## <a name="best-practices"></a>모범 사례

- 대기 시간과 관련된 잠재적 문제를 방지하려면 앱과 Azure Storage 계정을 동일한 Azure 지역에 배치합니다. 그러나 앱과 Azure Storage 계정이 동일한 Azure 지역에 있고 [Azure Storage 방화벽 구성](../storage/common/storage-network-security.md)에서 App Service IP 주소로 액세스 권한을 부여하는 경우 이러한 IP 제한이 적용되지 않습니다.
::: zone pivot="container-windows"
- 사용자 지정 컨테이너의 탑재 디렉터리는 비어 있어야 합니다. Azure Storage 탑재 될 때이 경로에 저장 된 콘텐츠는 삭제 됩니다. 기존 앱에 대한 파일을 마이그레이션하는 경우 시작하기 전에 앱과 앱의 콘텐츠를 백업하세요.
::: zone-end
::: zone pivot="container-linux"
- 사용자 지정 컨테이너의 탑재 디렉터리는 비어 있어야 합니다. 이 경로에 저장된 모든 콘텐츠는 Azure Storage가 탑재될 때 삭제됩니다(예: `/home` 아래에 있는 디렉터리를 지정하는 경우). 기존 앱에 대한 파일을 마이그레이션하는 경우 시작하기 전에 앱과 앱의 콘텐츠를 백업하세요.

- 스토리지를 `/home`에 탑재하면 앱의 성능 병목 현상이 발생할 수 있으므로 권장하지 않습니다. 
::: zone-end
- Azure Storage 계정에서, 앱에 스토리지를 탑재하는 데 사용되는 [액세스 키를 다시 생성](../storage/common/storage-account-keys-manage.md)하지 않습니다. 스토리지 계정에는 두 개의 서로 다른 키가 포함되어 있습니다. 단계별 접근 방식을 사용하여 키를 다시 생성하는 동안 스토리지 탑재를 앱에서 계속 사용할 수 있도록 합니다. 예를 들어 **key1** 을 사용하여 앱에서 스토리지 탑재를 구성하는 경우 다음을 수행합니다.

    1. **key2** 를 다시 생성합니다. 
    1. 스토리지 탑재 구성에서, 다시 생성한 **key2** 를 사용하도록 액세스 키를 업데이트합니다.
    1. **key1** 을 다시 생성합니다.

- Azure Storage 계정, 컨테이너 또는 공유를 삭제할 때 오류 시나리오를 방지하기 위해 앱에서 해당하는 스토리지 탑재 구성을 제거합니다. 

- 탑재된 Azure Storage 계정은 표준 또는 프리미엄 성능 계층입니다. 앱 용량 및 처리량 요구 사항에 따라 스토리지 계정에 적합한 성능 계층을 선택합니다. 스토리지 유형에 해당하는 확장성 및 성능 목표를 참조하세요.

    - 파일 (Windows 및 Linux 컨테이너) [의 경우](../storage/files/storage-files-scale-targets.md)
    - [Blob의 경우](../storage/blobs/scalability-targets.md) (Linux 컨테이너에만 해당)

- 앱이 [여러 인스턴스로 스케일링](../azure-monitor/autoscale/autoscale-get-started.md)되면 모든 인스턴스가 탑재된 동일한 Azure Storage 계정에 연결됩니다. 성능 병목 상태 및 처리량 문제를 방지할 수 있도록 스토리지 계정에 적합한 성능 계층을 선택합니다.  

- 로컬 데이터베이스(예: SQLite) 또는 파일 핸들과 잠금을 사용하는 다른 애플리케이션 및 구성 요소에는 스토리지 탑재를 사용하지 않는 것이 좋습니다. 

- 앱에서 Azure Storage [프라이빗 엔드포인트](../storage/common/storage-private-endpoints.md)를 사용하는 경우 다음과 같은 두 가지 앱 설정을 지정해야 합니다.

    - `WEBSITE_DNS_SERVER` = `168.63.129.16`
    - `WEBSITE_VNET_ROUTE_ALL` = `1`

- [스토리지 장애 조치(failover)를 시작](../storage/common/storage-initiate-account-failover.md)하고 스토리지 계정이 앱에 탑재되면 사용자가 앱을 다시 시작하거나 Azure Storage 탑재를 제거하고 다시 추가할 때까지 탑재가 연결되지 않습니다. 

## <a name="next-steps"></a>다음 단계

::: zone pivot="container-windows"

- [사용자 지정 컨테이너를 사용하여 Azure App Service로 사용자 지정 소프트웨어 마이그레이션](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [사용자 지정 컨테이너 구성](configure-custom-container.md?pivots=platform-linux)
- [비디오: 로컬 공유로 Azure Storage를 탑재 하는 방법](https://www.youtube.com/watch?v=OJkvpWYr57Y)

::: zone-end
