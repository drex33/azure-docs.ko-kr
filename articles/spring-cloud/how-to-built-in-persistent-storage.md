---
title: Azure Spring Cloud | 기본 제공 영구 스토리지를 사용하는 방법 Microsoft Docs
description: Azure Spring Cloud 기본 제공 영구 스토리지를 사용하는 방법
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b56932b9638ede2b3db36050310a06921d115103
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482999"
---
# <a name="use-built-in-persistent-storage-in-azure-spring-cloud"></a>Azure Spring Cloud 기본 제공 영구 스토리지 사용

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

Azure Spring Cloud 애플리케이션에 대한 두 가지 유형의 기본 제공 스토리지인 영구 및 임시 스토리지를 제공합니다.

기본적으로 Azure Spring Cloud는 각 애플리케이션 인스턴스에 대해 임시 스토리지를 제공합니다. 임시 스토리지는 인스턴스별로 기본 탑재 경로가 /tmp인 5GB로 제한됩니다.

> [!WARNING]
> 애플리케이션 인스턴스를 다시 시작하면 연결된 임시 스토리지가 영구적으로 삭제됩니다.

영구 스토리지는 Azure에서 관리되며 애플리케이션마다 할당되는 파일 공유 컨테이너입니다. 영구적 스토리지에 저장된 데이터는 애플리케이션의 모든 인스턴스에서 공유됩니다. Azure Spring Cloud 인스턴스는 영구 스토리지를 사용하는 애플리케이션을 최대 10개까지 포함할 수 있습니다. 각 애플리케이션에는 50GB의 영구 스토리지가 할당됩니다. 영구 스토리지에 대한 기본 탑재경로는 /persistent입니다.

> [!WARNING]
> 애플리케이션의 영구 스토리지를 사용하지 않도록 설정하면 모든 스토리지의 할당이 취소되고 저장된 모든 데이터가 손실됩니다.

## <a name="enable-or-disable-built-in-persistent-storage"></a>기본 제공 영구 스토리지 사용 또는 사용 안 함

Azure Portal 사용하거나 Azure CLI 사용하여 기본 제공 영구 스토리지의 상태를 수정할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)
## <a name="enable-or-disable-built-in-persistent-storage-with-the-portal"></a>포털에서 기본 제공 영구 스토리지 사용 또는 사용 안 함
포털을 사용하여 기본 제공 영구 스토리지를 사용하거나 사용하지 않도록 설정할 수 있습니다.

1. Azure Portal **홈** 페이지에서 **모든 리소스** 를 선택합니다.

    >![모든 리소스 아이콘 찾기](media/portal-all-resources.jpg)

1. 영구 스토리지를 필요로 하는 Azure Spring Cloud 리소스를 선택합니다. 이 예제에서는 선택한 애플리케이션을 **upspring** 이라고 합니다.

    > ![애플리케이션을 선택합니다.](media/select-service.jpg)

1. **설정** 제목 아래에서 **앱** 을 선택합니다.

1. Azure Spring Cloud 서비스는 테이블에 표시됩니다.  영구 스토리지를 추가할 서비스를 선택합니다. 이 예제에서는 **게이트웨이** 서비스를 선택합니다.

    > ![서비스 선택](media/select-gateway.jpg)

1. 서비스의 구성 페이지에서 **구성** 을 선택합니다.

1. 영구 **Storage** 탭을 선택하고 **사용을** 선택하여 영구 스토리지를 켜거나 **사용 안 함을** 선택하여 영구 스토리지를 해제합니다.

    > ![영구 스토리지 사용하도록 설정](media/enable-persistent-storage.jpg)

영구 스토리지를 사용하도록 설정하면 해당 크기 및 경로가 **영구 Storage** 탭에 표시됩니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
## <a name="use-the-azure-cli-to-enable-or-disable-built-in-persistent-storage"></a>Azure CLI 사용하여 기본 제공 영구 스토리지를 사용하거나 사용하지 않도록 설정합니다.
필요한 경우 다음 명령을 사용하여 Azure CLI 대한 Spring Cloud 확장을 설치합니다.

```azurecli
az extension add --name spring-cloud
```

기타 작업:

* 기본 제공 영구 스토리지를 사용하도록 설정된 앱을 만들려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 기존 앱에 기본 제공 영구 스토리지를 사용하도록 설정하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* 기존 앱에서 기본 제공 영구 스토리지를 사용하지 않도록 설정하려면 다음을 수행합니다.

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```
---
> [!WARNING]
> 애플리케이션의 영구 스토리지를 사용하지 않도록 설정하면 모든 스토리지의 할당이 취소되고 저장된 모든 데이터가 영구적으로 손실됩니다.

## <a name="next-steps"></a>다음 단계

* [애플리케이션 및 서비스 할당량](./quotas.md)에 대해 자세히 알아봅니다.
* [수동으로 애플리케이션 크기를 조정](./how-to-scale-manual.md)하는 방법을 알아봅니다.
