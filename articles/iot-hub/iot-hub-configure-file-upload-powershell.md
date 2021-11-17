---
title: Azure PowerShell을 사용하여 파일 업로드 구성 | Microsoft Docs
description: Azure PowerShell cmdlet을 사용하여 연결된 디바이스에서 파일 업로드를 사용하도록 IoT Hub를 구성하는 방법입니다. 대상 Azure Storage 계정 구성에 대한 정보가 포함됩니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: lizross
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f20236edd7a905f05d59ddafd3132569a851816a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553936"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Azure PowerShell을 사용하여 IoT Hub 파일 업로드 구성

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

이 문서에서는 PowerShell을 사용하여 IoT Hub에서 파일 업로드를 구성하는 방법을 보여 줍니다. 

[IoT Hub의 파일 업로드 기능](iot-hub-devguide-file-upload.md)을 사용하려면 먼저 Azure Storage 계정 및 Blob 컨테이너를 IoT Hub에 연결해야 합니다. IoT Hub는 파일을 업로드하는 경우에 사용할 디바이스에 대한 이 Blob 컨테이너에 쓰기 권한이 있는 SAS URI를 자동으로 생성합니다. 스토리지 계정 및 Blob 컨테이너 외에도 SAS URI에 대한 TTL(time-to-live)을 설정하고 IoT Hub가 백 엔드 서비스에 전달할 수 있는 선택적 파일 업로드 알림에 대한 설정을 구성할 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* Azure IoT Hub - IoT Hub가 없는 경우 [New-AzIoTHub cmdlet](/powershell/module/az.iothub/new-aziothub)을 사용하여 IoT Hub를 만들거나, 포털을 사용하여 [IoT Hub를 만들](iot-hub-create-through-portal.md) 수 있습니다.

* Azure 스토리지 계정. Azure 스토리지 계정이 없는 경우 [Azure Storage PowerShell cmdlet](/powershell/module/az.storage/)을 사용하여 스토리지 계정을 만들거나, 포털을 사용하여 [스토리지 계정을 만들](../storage/common/storage-account-create.md) 수 있습니다.

* [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)에서 PowerShell 환경을 사용합니다.

   [![새 창에서 Cloud Shell 시작](./media/iot-hub-configure-file-upload-powershell/hdi-launch-cloud-shell.png)](https://shell.azure.com)

* 원하는 경우 PowerShell을 로컬로 [설치](/powershell/scripting/install/installing-powershell)합니다.

  * [Azure Az PowerShell 모듈을 설치합니다](/powershell/azure/install-az-ps). (모듈은 기본적으로 Azure Cloud Shell PowerShell 환경에 설치됩니다.) 
  * [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 PowerShell에 로그인합니다.  인증 프로세스를 완료하려면 터미널에 표시되는 단계를 수행합니다.  추가 로그인 옵션은 [Azure PowerShell을 사용하여 로그인](/powershell/azure/authenticate-azureps)을 참조하세요.


## <a name="sign-in-and-set-your-azure-account"></a>Azure 계정 로그인 및 설정

Azure 계정에 로그인하고 구독을 선택합니다. Azure Cloud Shell을 사용하는 경우 이미 로그인되어 있어야 합니다. 그러나 여러 구독이 있는 경우 여전히 Azure 구독을 선택해야 할 수 있습니다.

1. PowerShell 프롬프트에서 **Connect-AzAccount** cmdlet을 실행합니다.

    ```powershell
    Connect-AzAccount
    ```

2. Azure 구독이 여러 개 있는 경우 Azure에 로그인하면 자격 증명과 연결된 모든 Azure 구독에 액세스할 수 있습니다. [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) 명령을 사용하여 사용할 수 있는 Azure 구독을 나열합니다.

    ```powershell
    Get-AzSubscription
    ```

    다음 명령을 사용하여 IoT Hub를 관리하는 명령을 실행하는 데 사용할 구독을 선택합니다. 이전 명령의 출력에서 구독 이름 또는 ID를 사용할 수 있습니다.

    ```powershell
    Select-AzSubscription `
        -Name "{your subscription name}"
    ```

    > [!NOTE]
    > **Select-AzSubscription** 명령은 **Select-AzContext** 명령어에 필요한 더 복잡한 컨텍스트 이름이 아니라 **Get-AzSubscription** 명령어에 의해 반환된 구독 이름(**이름**) 또는 구독 ID(**Id** )를 사용하도록 허용하는 [Select-AzContext](/powershell/module/az.accounts/select-azcontext)의 별칭입니다.

## <a name="retrieve-your-storage-account-details"></a>스토리지 계정 세부 정보 검색

다음 단계에서는 **클래식** 배포 모델이 아니라 **Resource Manager** 배포 모델을 사용하여 스토리지 계정을 만들었다고 가정합니다.

디바이스에서 파일 업로드를 구성하려면 Azure Storage 계정에 대한 연결 문자열이 필요합니다. 스토리지 계정은 IoT Hub와 동일한 구독 내에 있어야 합니다. 또한 스토리지 계정에 Blob 컨테이너의 이름도 필요합니다. [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) 명령을 사용하여 스토리지 계정 키를 검색합니다.

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

**key1** 스토리지 계정 키 값을 기록해 둡니다. 다음 단계에서 필요합니다.

파일 업로드에 기존 Blob 컨테이너를 사용하거나 새 Blob 컨테이너를 만들 수 있습니다.

* 스토리지 계정의 기존 Blob 컨테이너를 나열하려면 [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) 및 [Get-AzStorageContainer](/powershell/module/az.storage/get-azstoragecontainer) 명령을 사용합니다.

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* 스토리지 계정에 Blob 컨테이너를 만들려면 [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) 및 [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) 명령을 사용합니다.

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>IoT Hub 구성

이제 스토리지 계정 세부 정보를 사용하여 [IoT Hub에 파일을 업로드](iot-hub-devguide-file-upload.md)하도록 IoT Hub를 구성할 수 있습니다.

구성에는 다음 값이 필요합니다.

* **스토리지 컨테이너**: 현재 Azure 구독에 있는 Azure Storage 계정의 Blob 컨테이너로 IoT Hub와 연결됩니다. 이전 섹션에서 필요한 스토리지 계정 정보를 검색했습니다. IoT Hub는 파일을 업로드하는 경우에 사용할 디바이스에 대한 이 Blob 컨테이너에 쓰기 권한이 있는 SAS URI를 자동으로 생성합니다.

* **업로드된 파일에 대한 알림 받기**: 파일 업로드 알림을 사용하거나 사용하지 않도록 설정합니다.

* **SAS TTL**: 이 설정은 IoT Hub에서 디바이스로 반환하는 SAS URI의 TTL(Time-to-Live)입니다. 기본적으로 1시간으로 설정합니다.

* **파일 알림 설정 기본 TTL**: 만료되기 전의 파일 업로드 알림 TTL입니다. 기본적으로 1일로 설정합니다.

* **파일 알림 최대 배달 횟수**: IoT Hub가 파일 업로드 알림 배달을 시도하는 횟수입니다. 기본적으로 10으로 설정합니다.

[Set-AzIotHub](/powershell/module/az.iothub/set-aziothub) 명령을 사용하여 IoT Hub에서 파일 업로드 설정을 구성합니다.

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

> [!NOTE]
> 기본적으로 IoT Hub는 연결 문자열의 계정 키를 사용하여 Azure Storage로 인증합니다. 시스템 할당 또는 사용자 할당 관리 ID를 사용한 인증도 사용할 수 있습니다. 관리 ID는 Azure AD에서 자동으로 관리되는 ID를 안전한 방식으로 Azure 서비스에 제공합니다. 자세한 내용은 [관리 ID에 대한 IoT Hub 지원](./iot-hub-managed-identity.md)을 참조하세요. 현재 **Set-AzIotHub** 명령에 인증 유형을 설정하는 매개 변수가 없습니다. 대신 [Azure Portal](./iot-hub-configure-file-upload.md) 또는 [Azure CLI](./iot-hub-configure-file-upload-cli.md)를 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [디바이스 개요에서 파일 업로드](iot-hub-devguide-file-upload.md)
* [관리 ID에 대한 IoT Hub 지원](./iot-hub-managed-identity.md)
* [파일 업로드 방법 가이드](./iot-hub-csharp-csharp-file-upload.md)