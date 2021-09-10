---
title: Azure CLI를 사용하여 IoT Hub에 파일 업로드 구성 | Microsoft Docs
description: 플랫폼 간 Azure CLI를 사용하여 Azure IoT Hub에 파일 업로드를 구성하는 방법입니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: robinsh
ms.openlocfilehash: ecaae482a0a577efd480610f25d0186502dbeb15
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566709"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Azure CLI를 사용하여 IoT Hub 파일 업로드 구성

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

이 문서에서는 Azure CLI를 사용하여 IoT 허브에서 파일 업로드를 구성하는 방법을 보여 줍니다. 

[IoT Hub의 파일 업로드 기능](iot-hub-devguide-file-upload.md)을 사용하려면 먼저 Azure Storage 계정 및 Blob 컨테이너를 IoT 허브에 연결해야 합니다. IoT Hub는 파일을 업로드하는 경우에 사용할 디바이스에 대한 이 Blob 컨테이너에 쓰기 권한이 있는 SAS URI를 자동으로 생성합니다. 스토리지 계정 및 Blob 컨테이너 외에도 SAS URI의 TTL(Time to Live) 및 IoT Hub가 Azure Storage에서 사용하는 인증 유형을 설정할 수 있습니다. IoT Hub가 백 엔드 서비스에 제공할 수 있는 선택적 파일 업로드 알림에 대한 설정도 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* Azure IoT Hub - IoT Hub가 없는 경우 [`az iot hub create` 명령](/cli/azure/iot/hub#az_iot_hub_create)을 사용하여 하나 만들거나 [포털을 사용하여 IoT Hub를 만들 수 있습니다](iot-hub-create-through-portal.md).

* Azure Storage 계정. Azure Storage 계정이 없는 경우 Azure CLI를 사용하여 계정을 만들 수 있습니다. 자세한 내용은 [스토리지 계정 만들기](../storage/common/storage-account-create.md) 를 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Azure 계정 로그인 및 설정

Azure 계정에 로그인하고 구독을 선택합니다. Azure Cloud Shell을 사용하는 경우 이미 로그인되어 있어야 합니다. 그러나 여러 구독이 있는 경우에는 Azure 구독을 선택해야 할 수 있습니다.

1. 명령 프롬프트에서 [login 명령](/cli/azure/get-started-with-azure-cli)을 실행합니다.

    ```azurecli
    az login
    ```

    지침에 따라 코드를 사용하여 인증하고 웹 브라우저를 통해 Azure 계정에 로그인합니다.

2. 여러 Azure 구독이 있는 경우 Azure에 로그인하면 자격 증명과 연결된 모든 Azure 계정에 대한 액세스를 허용합니다. 다음 [Azure 계정을 나열하는 명령](/cli/azure/account)을 사용합니다.

    ```azurecli
    az account list
    ```

    다음 명령을 사용하여 IoT Hub를 만드는 명령을 실행하는 데 사용할 구독을 선택합니다. 이전 명령의 출력에서 구독 이름 또는 ID를 사용할 수 있습니다.

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>스토리지 계정 세부 정보 검색

다음 단계에서는 **클래식** 배포 모델이 아니라 **Resource Manager** 배포 모델을 사용하여 스토리지 계정을 만들었다고 가정합니다.

디바이스에서 파일 업로드를 구성하려면 Azure Storage 계정에 대한 연결 문자열이 필요합니다. 스토리지 계정은 IoT Hub와 동일한 구독 내에 있어야 합니다. 또한 스토리지 계정에 Blob 컨테이너의 이름도 필요합니다. 다음 명령을 사용하여 스토리지 계정 키를 검색합니다.

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```
연결 문자열은 다음 출력과 유사합니다.

```json
{
  "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={your storage account name};AccountKey={your storage account key}"
}
```

`connectionString` 값을 기록해 둡니다. 다음 단계에서 필요합니다.

파일 업로드에 기존 Blob 컨테이너를 사용하거나 새 Blob 컨테이너를 만들 수 있습니다.

* 스토리지 계정의 기존 Blob 컨테이너를 나열하려면 다음 명령을 사용합니다.

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* 스토리지 계정에 Blob 컨테이너를 만들려면 다음 명령을 사용합니다.

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="configure-your-iot-hub"></a>IoT Hub 구성

이제 스토리지 계정 세부 정보를 사용하여 [IoT Hub에 파일을 업로드](iot-hub-devguide-file-upload.md)하는 기능을 설정하도록 IoT Hub를 구성할 수 있습니다.

구성에는 다음 값이 필요합니다.

* **스토리지 컨테이너**: 현재 Azure 구독에 있는 Azure Storage 계정의 Blob 컨테이너로 IoT Hub와 연결됩니다. 이전 섹션에서 필요한 스토리지 계정 정보를 검색했습니다. IoT Hub는 파일을 업로드하는 경우에 사용할 디바이스에 대한 이 Blob 컨테이너에 쓰기 권한이 있는 SAS URI를 자동으로 생성합니다.

* **업로드된 파일에 대한 알림 받기**: 파일 업로드 알림을 사용하거나 사용하지 않도록 설정합니다.

* **SAS TTL**: 이 설정은 IoT Hub에서 디바이스로 반환하는 SAS URI의 TTL(Time-to-Live)입니다. 기본적으로 1시간으로 설정합니다.

* **파일 알림 설정 기본 TTL**: 만료되기 전 파일 업로드 알림의 TTL(Time to Live)입니다. 기본적으로 1일로 설정합니다.

* **파일 알림 최대 배달 횟수**: IoT Hub가 파일 업로드 알림 배달을 시도하는 횟수입니다. 기본적으로 10으로 설정합니다.

* **파일 알림 잠금 기간**: 파일 알림 큐의 잠금 기간입니다. 기본적으로 60초로 설정됩니다.

* **인증 유형**: Azure Storage에 사용할 IoT Hub의 인증 유형입니다. 이 설정은 IoT 허브가 Azure Storage에서 인증하고 권한을 부여하는 방법을 결정합니다. 기본값은 키 기반 인증입니다. 그러나 시스템 및 사용자가 할당한 관리 ID를 사용할 수도 있습니다. 관리 ID는 Azure AD에서 자동으로 관리되는 ID를 안전한 방식으로 Azure 서비스에 제공합니다. IoT 허브 및 Azure Storage 계정에서 관리 ID를 구성하는 방법을 알아보려면 [관리 ID에 대한 IoT Hub 지원](./iot-hub-managed-identity.md)을 참조하세요. 구성되면 Azure Storage를 사용한 인증에 관리 ID 중 하나를 사용할 수 있습니다.

    > [!NOTE]
    > 인증 유형 설정은 IoT 허브가 Azure Storage 계정으로 인증하는 방법을 구성합니다. 디바이스는 항상 IoT 허브에서 가져온 SAS URI를 사용하여 Azure Storage에서 인증합니다. 


다음 명령은 IoT 허브에서 파일 업로드 설정을 구성하는 방법을 보여 줍니다. 이러한 명령은 명확하게 하기 위해 별도로 표시되지만 일반적으로 시나리오에 필요한 모든 매개 변수를 사용하여 단일 명령을 실행합니다. 명령줄에 표시되는 위치에 따옴표를 포함하고 중괄호를 포함하지 마세요. 각 매개 변수에 대한 자세한 내용은 [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update) 명령에 대한 Azure CLI 설명서에서 확인할 수 있습니다.

다음 명령은 스토리지 계정 및 Blob 컨테이너를 구성합니다.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-connectionstring "{your storage account connection string}" \
    --fileupload-storage-container-name "{your container name}" 
```

다음 명령은 SAS URI TTL(Time to Live)을 기본값(1시간)으로 설정합니다.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-sas-ttl 1 
```

다음 명령은 파일 알림을 사용하도록 설정하고 파일 알림 속성을 기본값으로 설정합니다. 파일 업로드 알림 TTL(Time to Live)은 1시간으로 설정되고 잠금 기간은 60초로 설정됩니다.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-notifications true  \
    --fileupload-notification-max-delivery-count 10 \
    --fileupload-notification-ttl 1 \
    --fileupload-notification-lock-duration 60
```

다음 명령은 키 기반 인증을 구성합니다.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type keyBased
```

다음 명령은 IoT 허브의 시스템이 할당한 관리 ID를 사용하여 인증을 구성합니다. 이 명령을 실행하려면 먼저 IoT 허브에 대해 시스템이 할당한 관리 ID를 사용하도록 설정하고 Azure Storage 계정에서 올바른 RBAC 역할을 부여해야 합니다. 방법을 알아보려면 [관리 ID에 대한 IoT Hub 지원](./iot-hub-managed-identity.md)을 참조하세요.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity [system] 
```

다음 명령은 IoT 허브에 구성된 사용자가 할당한 관리 ID를 검색하고 그중 하나를 사용하여 인증을 구성합니다. 사용자가 할당한 관리 ID를 사용하여 인증하려면 먼저 IoT 허브에서 구성하고 Azure Storage 계정에서 적절한 RBAC 역할을 부여해야 합니다. 자세한 내용 및 단계는 [관리 ID에 대한 IoT Hub 지원](./iot-hub-managed-identity.md)을 참조하세요.

IoT 허브에서 사용자가 할당한 관리 ID를 쿼리하려면 [az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show) 명령을 사용합니다.

```azurecli
az iot hub identity show --name {your iot hub name} --query userAssignedIdentities
```

이 명령은 IoT 허브에 구성된 사용자가 할당한 관리 ID 컬렉션을 반환합니다. 다음 출력에서는 사용자가 할당한 관리 ID 하나를 포함하는 컬렉션을 보여 줍니다.

```json
{
  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}": 
  {
    "clientId": "<client ID GUID>",
    "principalId": "<principal ID GUID>"
  }
}
```

다음 명령은 위의 사용자가 할당한 관리 ID를 사용하도록 인증을 구성합니다.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}"
```

다음 명령을 사용하여 IoT 허브의 설정을 검토할 수 있습니다.

```azurecli
az iot hub show --name {your iot hub name}
```

파일 업로드 설정만 검토하려면 다음 명령을 사용합니다.

```azurecli
az iot hub show --name {your iot hub name}
    --query '[properties.storageEndpoints, properties.enableFileUploadNotifications, properties.messagingEndpoints.fileNotifications]'
```

대부분의 경우 Azure CLI 명령에서 명명된 매개 변수를 사용하기가 가장 쉽습니다. 그러나 `--set` 매개 변수를 사용하여 파일 업로드 설정을 구성할 수도 있습니다. 다음 명령을 사용하여 방법을 이해할 수 있습니다. 

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

## <a name="next-steps"></a>다음 단계

* [디바이스에서 파일 업로드 개요](iot-hub-devguide-file-upload.md)
* [관리 ID에 대한 IoT Hub 지원](./iot-hub-managed-identity.md)
* [파일 업로드 방법 가이드](./iot-hub-csharp-csharp-file-upload.md)
* Azure CLI [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update), [az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show) 및 [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 명령
