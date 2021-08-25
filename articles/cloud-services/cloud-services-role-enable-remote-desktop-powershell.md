---
title: PowerShell을 사용하여 역할에 대해 원격 데스크톱 사용
description: 원격 데스크톱 연결을 허용하기 위해 PowerShell을 사용하여 Azure 클라우드 서비스 애플리케이션을 구성하는 방법
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3ebc3cf6a576a15c482120017ea14f26e376c6f6
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823213"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-powershell"></a>PowerShell을 사용하여 Azure Cloud Services(클래식)의 역할에 대해 원격 데스크톱 연결 사용

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

원격 데스크톱을 사용하면 Azure에서 실행 중인 역할의 데스크톱에 액세스할 수 있습니다. 원격 데스크톱 연결을 사용하여 애플리케이션 실행 중에 애플리케이션 문제를 진단하고 해결할 수 있습니다.

이 문서에서는 PowerShell을 사용하여 클라우드 서비스 역할에 대해 원격 데스크톱을 사용하는 방법을 설명합니다. 이 문서에 필요한 필수 조건은 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/) 을 참조하세요. PowerShell에서는 애플리케이션이 배포된 후 원격 데스크톱을 사용 가능하게 설정할 수 있도록 원격 데스크톱 확장을 사용합니다.

## <a name="configure-remote-desktop-from-powershell"></a>PowerShell에서 원격 데스크톱 구성
[Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) cmdlet을 사용하면 지정된 역할 또는 클라우드 서비스 배포의 모든 역할에 대해 원격 데스크톱을 사용할 수 있습니다. cmdlet을 사용하면 PSCredential 개체를 수용하는 *Credential* 매개 변수를 통해 원격 데스크톱 사용자의 사용자 이름 및 암호를 지정할 수 있습니다.

대화형으로 PowerShell을 사용하는 경우, [Get-Credentials](/powershell/module/microsoft.powershell.security/get-credential) cmdlet을 호출하여 PSCredential 개체를 쉽게 설정할 수 있습니다.

```powershell
$remoteusercredentials = Get-Credential
```

이 명령은 안전하게 원격 사용자의 사용자 이름과 암호를 입력할 수 있는 대화 상자를 표시합니다.

PowerShell은 자동화 시나리오에 유용하므로 사용자 조작이 필요하지 않은 방식으로 **PSCredential** 개체를 설정할 수도 있습니다. 먼저 보안 암호를 설정해야 합니다. 일반 텍스트 암호를 지정하는 것으로 시작하고 [Convertto-securestring](/powershell/module/microsoft.powershell.security/convertto-securestring)을 사용하여 보안 문자열로 변환합니다. 그런 다음 이 보안 문자열을 [ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring)을 사용하여 암호화된 표준 문자열로 변환해야 합니다. 이제 이 암호화된 표준 문자열을 [Set-Content](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176959(v=technet.10))를 사용하여 파일로 저장할 수 있습니다.

또한 매번 암호를 입력하지 않아도 되도록 보안 암호 파일을 만들 수 있습니다. 보안 암호 파일은 일반 텍스트 파일보다 더 좋습니다. 다음과 같은 PowerShell을 사용하여 보안 암호 파일을 만듭니다.

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> 암호를 설정할 때 [복잡성 요구 사항](/previous-versions/windows/it-pro/windows-server-2003/cc786468(v=ws.10))을 충족하는지 확인합니다.

보안 암호 파일에서 자격 증명 개체를 만들려면 파일 내용을 읽고 [Convertto-securestring](/powershell/module/microsoft.powershell.security/convertto-securestring)을 사용하여 이를 다시 보안 문자열로 변환해야 합니다.

[Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) cmdlet은 사용자 계정이 만료되는 *DateTime* 을 지정하는 **Expiration** 매개 변수를 수락하기도 합니다. 예를 들어 현재 날짜 및 시간부터 며칠 후에 만료되도록 계정을 설정할 수 있습니다.

이 PowerShell 예는 클라우드 서비스에서 원격 데스크톱 확장을 설정하는 방법을 보여줍니다.

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
또한 원격 데스크톱을 사용하려는 배포 슬롯와 역할을 선택적으로 지정할 수도 있습니다. 이러한 매개 변수가 지정되지 않은 경우 cmdlet은 **프로덕션** 배포 슬롯의 모든 역할에 대해 원격 데스크톱을 사용하도록 설정합니다.

원격 데스크톱 확장은 배포와 연결됩니다. 서비스에 대한 새 배포를 만드는 경우 해당 배포에 대해 원격 데스크톱을 사용하도록 설정해야 합니다. 원격 데스크톱을 항상 사용하려는 경우 PowerShell 스크립트를 배포 워크플로에 통합하는 것이 좋습니다.

## <a name="remote-desktop-into-a-role-instance"></a>원격 데스크톱을 역할 인스턴스로 가져오기

[Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) cmdlet은 원격 데스크톱을 클라우드 서비스의 특정 역할 인스턴스에 가져오는 데 사용됩니다. *LocalPath* 매개 변수를 사용하여 RDP 파일을 로컬로 다운로드할 수 있습니다. 또는 *Launch* 매개 변수를 사용하여 클라우드 서비스 역할 인스턴스에 액세스하기 위해 원격 데스크톱 연결 대화 상자를 바로 시작할 수 있습니다.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>서비스에서 원격 데스크톱 확장을 사용할 수 있는지 확인합니다.

[Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) cmdlet은 원격 데스크톱이 서비스 배포에 사용되는지 여부를 표시합니다. 이 cmdlet은 원격 데스크톱 확장을 사용할 수 있는 원격 데스크톱 사용자 및 역할에 대한 사용자 이름을 반환합니다. 기본적으로 이는 배포 슬롯에서 발생하며, 대신 스테이징 슬롯을 사용하도록 선택할 수 있습니다.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>서비스에서 원격 데스크톱 확장 제거

배포에 대해 원격 데스크톱 확장을 이미 확장하고 원격 데스크톱 설정을 업데이트해야 하는 경우, 먼저 확장을 제거한 다음 새 설정으로 다시 사용하도록 설정해야 합니다. 예를 들어 원격 사용자 계정에 대한 새 암호를 설정하려는 경우 또는 계정이 만료된 경우가 여기에 해당합니다. 이는 원격 데스크톱 확장을 사용하도록 설정된 기존 배포에 필요합니다. 새 배포의 경우 확장을 바로 적용할 수 있습니다.

배포에서 원격 데스크톱 확장을 제거하려면 [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension) cmdlet을 사용하면 됩니다. 또한 원격 데스크톱을 제거하려는  배포 슬롯와 역할을 선택적으로 지정할 수도 있습니다.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> 확장 구성을 완전히 제거하려면 *UninstallConfiguration* 매개 변수와 **remove** cmdlet을 호출해야 합니다.
>
> **UninstallConfiguration** 매개 변수는 서비스에 적용된 모든 확장 구성을 제거합니다. 모든 확장 구성은 서비스 구성과 연결됩니다. **UninstallConfiguration** 없이 *remove* cmdlet을 호출하면 확장 구성에서 <mark>배포</mark>가 분리되어 확장이 효과적으로 제거됩니다. 그러나 확장 구성은 서비스와 연결된 상태로 유지됩니다.

## <a name="additional-resources"></a>추가 리소스

[Cloud Services를 구성하는 방법](cloud-services-how-to-configure-portal.md)