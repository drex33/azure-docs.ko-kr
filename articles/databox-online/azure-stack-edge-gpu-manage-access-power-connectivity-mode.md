---
title: Azure Stack Edge Pro GPU 디바이스 액세스, 전원 및 연결 모드 | Microsoft Docs
description: Azure로 데이터를 전송하는 데 도움이 되는 Azure Stack Edge Pro GPU 디바이스에 대한 액세스, 전원 및 연결 모드를 관리하는 방법에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/08/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 97ba43602899ee60a6a2d24cdd699b12b8a1c687
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219712"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU에 대한 액세스, 전원 및 연결 모드 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 GPU 디바이스에서 Azure Stack Edge Pro에 대한 액세스, 전원 및 연결 모드를 관리하는 방법에 대해 설명합니다. 이러한 작업은 로컬 웹 UI 또는 Azure Portal을 통해 수행합니다.

이 문서는 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R, Azure Stack Edge Mini R 디바이스에만 적용됩니다.


이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 디바이스 액세스 관리
> * 리소스 액세스 관리
> * 연결 모드 관리
> * 전원 관리


## <a name="manage-device-access"></a>디바이스 액세스 관리

Azure Stack Edge Pro 디바이스에 대한 액세스는 디바이스 암호를 사용하여 제어됩니다. 로컬 웹 UI를 통해 암호를 변경할 수 있습니다. Azure Portal에서 디바이스 암호를 다시 설정할 수도 있습니다.

디바이스 디스크의 데이터에 대한 액세스는 미사용 암호화 키로도 제어됩니다. 

디바이스의 로컬 웹 UI에서 HTTP 또는 HTTPS를 통해 원격 PowerShell 세션을 열어 디바이스에 액세스할 수 있습니다.

### <a name="change-device-password"></a>디바이스 암호 변경

로컬 웹 UI에서 다음 단계에 따라 디바이스 암호를 변경합니다.

1. 로컬 웹 UI에서 **유지 관리 > 암호** 로 이동합니다.
2. 현재 암호를 입력한 다음, 새 암호를 입력합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. 새 암호를 확인합니다.

    ![암호 변경](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/change-password-1.png)

3. **암호 변경** 을 선택합니다.
 
### <a name="reset-device-password"></a>디바이스 암호 재설정

재설정 워크플로에서는 사용자가 이전 암호를 기억할 필요가 없으며 암호를 분실했을 때 유용합니다. 이 워크플로는 Azure Portal에서 수행합니다.

1. Azure Portal에서 **개요 > 관리자 암호 재설정** 으로 이동합니다.

    ![스크린샷은 디바이스 암호 초기화가 선택된 디바이스를 보여 줍니다.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/reset-password-1.png)


2. 새 암호를 입력하고 확인합니다. 제공된 암호는 8 ~ 16자 사이여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다. **재설정** 을 선택합니다.

    ![암호 재설정 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

### <a name="manage-access-to-device-data"></a>디바이스 데이터에 대한 액세스 관리

Azure Stack Edge Pro R 및 Azure Stack Edge Mini R 디바이스의 경우 디바이스 드라이브에 미사용 암호화 키를 사용하여 디바이스 데이터에 대한 액세스를 제어합니다. 미사용 암호화를 위해 디바이스를 성공적으로 구성한 후에는 디바이스의 로컬 UI에서 미사용 암호화 키 회전 옵션을 사용할 수 있게 됩니다. 

이 작업을 통해 BitLocker 볼륨 `HcsData`와 `HcsInternal` 및 디바이스의 모든 자체 암호화 드라이브에 대한 키를 변경할 수 있습니다.

다음 단계에 따라 미사용 암호화 키를 회전합니다.

1. 디바이스의 로컬 UI에서 **시작** 페이지로 이동합니다. **보안** 타일에서 **미사용 암호화: 키 회전** 옵션을 선택합니다. 이 옵션은 미사용 암호화 키를 성공적으로 구성한 후에만 사용할 수 있습니다.

    ![시작 페이지에서 미사용 암호화에 대한 키 회전을 선택합니다.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-1.png)

1. 사용자 고유의 BitLocker 키를 사용하거나 시스템 생성 키를 사용할 수 있습니다.  

    사용자 고유의 키를 제공하려면 Base-64로 인코딩된 32자 길이의 문자열을 입력합니다. 입력은 미사용 암호화를 처음으로 구성할 때 제공하는 것과 비슷합니다.

    ![사용자 고유의 미사용 암호화 키 가져오기](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-2.png)

    시스템 생성 키를 사용하도록 선택할 수도 있습니다.

    ![시스템에서 생성된 미사용 암호화 키 사용](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-3.png)

1. **적용** 을 선택합니다. 키 보호기가 회전됩니다.

    ![새 미사용 암호화 키 적용](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-4.png)

1. 키 파일을 다운로드하고 저장하라는 메시지가 표시되면 **다운로드 및 계속** 을 선택합니다. 

    ![키 파일 다운로드 및 계속](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-5.png)

    `.json` 키 파일을 안전한 위치에 저장합니다. 이 파일을 사용하면 디바이스의 잠재적인 향후 복구가 용이하게 됩니다.

    ![디바이스 암호 초기화 대화 상자를 보여 주는 스크린샷](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="enable-device-access-via-remote-powershell-over-http"></a>HTTP를 통해 원격 PowerShell을 통해 디바이스 액세스 사용

HTTP 또는 HTTPS를 통해 디바이스에 대한 원격 PowerShell 세션을 열 수 있습니다. 기본적으로 HTTPS를 통해 PowerShell 세션을 통해 디바이스에 액세스합니다. 그러나 신뢰할 수 있는 네트워크에서는 HTTP를 통해 원격 PowerShell을 사용하도록 설정할 수 있습니다.

로컬 UI에서 다음 단계를 수행하여 HTTP를 통한 원격 PowerShell을 사용하도록 설정합니다.

1. 디바이스의 로컬 UI에서 페이지 오른쪽 상단의 **설정** 으로 이동합니다.
1. HTTP를 통해 디바이스에 대한 원격 PowerShell 세션을 열 수 있도록 하려면 **사용** 을 선택합니다. 이 설정은 신뢰할 수 있는 네트워크에서만 사용해야 합니다.

    ![HTTP를 통한 원격 PowerShell 사용 설정을 보여 주는 스크린샷](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/enable-remote-powershell-http-1.png)

1. **적용** 을 선택합니다.

이제 HTTP를 통해 디바이스의 PowerShell 인터페이스에 연결할 수 있습니다. 자세한 내용은 [디바이스의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)을 참조하세요.

## <a name="manage-resource-access"></a>리소스 액세스 관리

Azure Stack Edge/Data Box 게이트웨이, IoT Hub 및 Azure Storage 리소스를 만들려면 리소스 그룹 수준에서 기여자 이상의 권한이 필요합니다. 또한 해당하는 리소스 공급자를 등록해야 합니다. 활성화 키 및 자격 증명을 포함하는 작업의 경우 Microsoft Graph API 사용 권한도 필요합니다. 이 내용은 다음 섹션에 설명되어 있습니다. 

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API 사용 권한 관리

Azure Stack Edge Pro 디바이스에 대한 활성화 키를 생성하거나 자격 증명이 필요한 작업을 수행하는 경우 Azure Active Directory Graph API 사용 권한이 필요합니다. 자격 증명을 필요로 하는 작업은 다음과 같습니다.

-  연결된 스토리지 계정을 사용하여 공유 만들기.
-  디바이스에서 공유에 액세스할 수 있는 사용자 만들기.

`Read all directory objects`가 가능해야 하므로 Active Directory 테넌트에 대한 `User` 액세스 권한이 필요합니다. 게스트 사용자는 `Read all directory objects` 권한이 없으므로 게스트이면 안 됩니다. 게스트인 경우 활성화 키 생성, Azure Stack Edge Pro 디바이스에 대한 공유 만들기, 사용자 만들기, Edge 컴퓨팅 역할 구성, 암호 재설정 등의 작업이 모두 실패합니다.

사용자에게 Microsoft Graph API에 대한 액세스 권한을 제공하는 방법에 대한 자세한 내용은 [Microsoft Graph 권한 참조](/graph/permissions-reference)를 참조하세요.

### <a name="register-resource-providers"></a>리소스 공급자 등록

Azure의 Azure Resource Manager 모델에서 리소스를 프로비저닝하려면 해당 리소스 만들기를 지원하는 리소스 공급자가 필요합니다. 예를 들어 가상 머신을 프로비저닝하려면 구독에서 'Microsoft.Compute' 리소스 공급자를 사용할 수 있어야 합니다.
 
리소스 공급자는 구독 수준에서 등록됩니다. 기본적으로 새 Azure 구독은 주로 사용되는 리소스 공급자 목록에 미리 등록됩니다. 'Microsoft.DataBoxEdge'의 리소스 공급자는 이 목록에 포함되지 않습니다.

사용자가 해당 리소스에 대한 리소스 공급자가 이미 등록되어 있는 경우 소유자 권한이 있는 리소스 그룹 내에서 'Microsoft.DataBoxEdge'와 같은 리소스를 만들기 위해 구독 수준에서 액세스 권한을 부여할 필요가 없습니다.

리소스를 만들기 전에 리소스 공급자가 구독에 등록되어 있는지 확인합니다. 리소스 공급자가 등록되지 않은 경우 새 리소스를 만드는 사용자에게 구독 수준에서 필요한 리소스 공급자를 등록할 수 있는 충분한 권한이 있는지 확인해야 합니다. 아직 수행하지 않은 경우 다음과 같은 오류가 표시됩니다.

*구독\<Subscription name>에 다음 리소스 공급자를 등록할 권한이 없습니다. Microsoft.DataBoxEdge.*


현재 구독에서 등록된 리소스 공급자 목록을 가져오려면 다음 명령을 실행합니다.

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Azure Stack Edge Pro 디바이스에 대해 `Microsoft.DataBoxEdge`를 등록해야 합니다. `Microsoft.DataBoxEdge`를 등록하려면 구독 관리자가 다음 명령을 실행해야 합니다.

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

리소스 공급자 등록 방법에 대한 자세한 내용은 [리소스 공급자 등록 오류 해결](../azure-resource-manager/templates/error-register-resource-provider.md)을 참조하세요.

## <a name="manage-connectivity-mode"></a>연결 모드 관리

디바이스는 완전 연결 모드 외에도, 부분 연결 또는 연결 끊김 모드에서도 실행할 수 있습니다. 이러한 각 모드는 아래에 설명되어 있습니다.

- **완전 연결** - 디바이스가 작동하는 일반적인 기본 모드입니다. 이 모드에서는 데이터의 클라우드 업로드와 다운로드를 모두 사용할 수 있습니다. Azure Portal 또는 로컬 웹 UI를 사용하여 디바이스를 관리할 수 있습니다. 

    > [!NOTE]
    > 네트워크 기능 관리자 배포의 경우 Azure Stack Edge 디바이스는 **온라인** 이고 완전 연결 모드에서 작동해야 합니다.

- **부분적으로 연결이 끊어짐** – 이 모드에서는 디바이스에서 공유 데이터를 업로드 또는 다운로드할 수 없지만 Azure Portal을 통해 디바이스를 관리할 수 있습니다.

    이 모드는 일반적으로 요금제 위성 네트워크에서 사용되며 목표는 네트워크 대역폭 소비를 최소화하는 것입니다. 디바이스 모니터링 작업을 위해 최소한의 네트워크 사용량이 여전히 발생할 수 있습니다.

- **연결 끊김** – 이 모드에서는 디바이스가 클라우드에서 연결이 완전히 끊어지고 클라우드 업로드와 다운로드를 모두 사용할 수 없습니다. 디바이스는 로컬 웹 UI를 통해서만 관리할 수 있습니다.

    이 모드는 일반적으로 디바이스를 오프라인 상태로 만들 때 사용합니다.

디바이스 모드를 변경하려면 다음 단계를 수행합니다.

1. 디바이스의 로컬 웹 UI에서 **구성 > 클라우드** 로 이동합니다.
2. 드롭다운 목록에서 디바이스를 작동할 모드를 선택합니다. **완전 연결**, **부분 연결** 및 **연결 끊김** 중에서 선택할 수 있습니다. 부분적으로 연결이 끊긴 모드에서 디바이스를 실행하려면 **Azure Portal 관리** 를 사용하도록 설정합니다.

 
## <a name="manage-power"></a>전원 관리

로컬 웹 UI를 사용하여 물리적 디바이스를 종료하거나 다시 시작할 수 있습니다. 다시 시작하기 전에 데이터 서버에서 공유를 오프라인으로 전환한 후 디바이스를 다시 시작하는 것이 좋습니다. 이 작업을 수행하면 데이터 손상 가능성이 최소화됩니다.

1. 로컬 웹 UI에서 **유지 관리 > 전원** 으로 이동합니다.
2. 수행하려는 작업에 따라 **종료** 또는 **다시 시작** 을 선택합니다.

    ![전원 설정](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 확인하라는 메시지가 표시되면 **예** 를 선택하여 계속합니다.

> [!NOTE]
> 물리적 디바이스를 종료한 경우 디바이스를 켜려면 전원 단추를 눌러야 합니다.

## <a name="next-steps"></a>다음 단계

- [공유 관리](azure-stack-edge-manage-shares.md) 방법에 대해 알아봅니다.
