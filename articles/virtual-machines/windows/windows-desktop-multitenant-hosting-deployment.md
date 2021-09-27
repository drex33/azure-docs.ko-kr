---
title: 다중 테넌트 호스팅 권한으로 Azure에서 Windows 10을 배포하는 방법
description: 다중 테넌트 호스팅 권한으로 Azure에 온-프레미스 라이선스를 가져오기 위해 Windows Software Assurance 혜택을 최대화하는 방법에 대해 알아봅니다.
author: mimckitt
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt, devx-track-azurepowershell
ms.openlocfilehash: 96cd1c079a4d3705dbddaffcaf23c44191e38543
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622420"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>다중 테넌트 호스팅 권한으로 Azure에서 Windows 10을 배포하는 방법 
**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합

Windows 10 Enterprise E3/E5 사용자 단위 또는 Windows Virtual Desktop Access 사용자 단위(사용자 구독 라이선스 또는 추가 기능 사용자 구독 라이선스)를 사용하는 사용자의 경우, Windows 10용 다중 테넌트 호스팅 권한을 사용하면 클라우드로 Windows 10 라이선스를 가져오고, 다른 라이선스에 비용을 지불하지 않으면서 Azure에서 Windows 10 Virtual Machines를 실행할 수 있습니다. 다중 테넌트 호스팅 권한은 Windows 10(버전 1703 이상)에서만 사용할 수 있습니다.

자세한 내용은 [Windows 10용 다중 테넌트 호스팅](https://www.microsoft.com/en-us/CloudandHosting)을 참조하세요.

> [!NOTE]
> - 개발 또는 테스트에 Windows 7, 8.1 및 10 이미지를 사용하려면 [개발/테스트 시나리오에 대한 Azure의 windows 클라이언트](client-images.md)를 참조하세요
> - Windows Server 라이선스 혜택은 [Windows Server 이미지에 Azure Hybrid를 사용하는 이점](hybrid-use-benefit-licensing.md)을 참조하세요.

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>다중 테넌트 호스팅 권한 자격이 있는 구독 라이선스

[Microsoft admin center](/microsoft-365/admin/admin-overview/about-the-admin-center)를 사용하여 사용자에게 Windows 10 지원 라이선스가 할당되었는지 확인할 수 있습니다.

> [!IMPORTANT]
> 사용자가 프로덕션 워크로드에 Azure의 Windows 10 이미지를 사용하려면 다음 구독 라이선스 중 하나가 **반드시** 있어야 합니다. 이러한 구독 라이선스 중 하나가 없는 경우 [클라우드 서비스 파트너](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)를 통하거나 [Microsoft](https://www.microsoft.com/microsoft-365?rtc=1)를 통해 직접 구매할 수 있습니다.

**적격 구독 라이선스:**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 A3/A5 
-   Windows 10 Enterprise E3/E5
-   Windows 10 Education A3/A5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Azure Marketplace에서 Windows 10 이미지 배포 
PowerShell, CLI 및 Azure Resource Manager 템플릿 배포의 경우 `PublisherName: MicrosoftWindowsDesktop` 및 `Offer: Windows-10`을 사용하여 Windows 10 이미지를 찾을 수 있습니다. 다중 테넌트 호스팅 권한에 대해 Windows 10 버전 작성자 업데이트(1809) 이상을 지원합니다. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

사용 가능한 이미지에 대한 자세한 내용은 [Azure PowerShell를 사용하여 AZURE MARKETPLACE VM 이미지 찾기 및 사용](./cli-ps-findimage.md)을  참조하세요

## <a name="uploading-windows-10-vhd-to-azure"></a>Azure에 Windows 10 VHD 업로드
일반화된 Windows 10 VHD를 업로드하는 경우 Windows 10에는 기본적으로 활성화된 기본 제공 관리자 계정이 없습니다. 기본 제공 관리자 계정을 사용하도록 설정하려면 사용자 지정 스크립트 확장의 일환으로 다음 명령을 포함합니다.

```powershell
Net user <username> /active:yes
```

다음 PowerShell 코드 조각은 기본 제공 관리자를 포함하여 모든 관리자 계정을 활성화로 표시하기 위한 것입니다. 이 예제는 기본 제공 관리자 사용자 이름을 알 수 없는 경우에 유용합니다.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
자세한 내용은 다음을 참조하세요. 
* [Azure에 VHD를 업로드하는 방법](upload-generalized-managed.md)
* [Azure에 업로드할 Windows VHD를 준비하는 방법](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>다중 테넌트 호스팅 권한으로 Windows 10 배포
먼저 [최신 Azure PowerShell을 설치 및 구성](/powershell/azure/)했는지 확인합니다. VHD를 준비했으면 다음과 같이 `Add-AzVhd` cmdlet을 사용하여 Azure Storage 계정에 VHD를 업로드합니다.

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Azure Resource Manager 템플릿 배포를 사용하여 배포** Resource Manager 템플릿 내에서 `licenseType`에 대한 추가 매개 변수는 지정할 수 있습니다. [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/templates/syntax.md)에 대해 자세히 알아볼 수 있습니다. Azure에 VHD를 업로드하고 나면 Resource Manager 템플릿을 편집하여 컴퓨팅 공급자의 일부로 라이선스 유형을 포함하고 정상적으로 템플릿을 배포합니다.
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**PowerShell을 통해 배포** PowerShell을 통해 Windows Server VM을 배포할 때 `-LicenseType`에 대한 추가 매개 변수가 있습니다. Azure에 VHD를 업로드하고 나면 다음과 같이 `New-AzVM`을 사용하여 VM을 만들고 라이선싱 형식을 지정합니다.
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM이 라이선싱 혜택을 사용하고 있는지 확인
PowerShell 또는 Resource Manager 배포 메서드를 통해 VM을 배포한 후 다음과 같이 `Get-AzVM` 을 사용하여 라이선스 형식을 확인합니다.
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

올바른 라이선스 형식이 있는 Windows 10의 경우 다음 예제와 유사하게 출력됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

이 출력은 Azure 갤러리에서 바로 배포된 VM과 같이 Azure 하이브리드 사용 혜택 라이선싱 없이 배포된 다음 VM과는 대조됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Azure AD 가입에 대한 추가 정보
Azure는 AAD 가입에 사용될 수 없는 기본 제공 관리자 계정을 사용하여 모든 Windows VM을 프로비전합니다. 예를 들어 *설정 > 계정 > 회사 또는 학교 액세스 > + 연결* 은 작동하지 않습니다. Azure AD에 수동으로 가입하려면 두 번째 관리자 계정을 만들어 로그온해야 합니다. 또한 프로비저닝 패키지를 사용하여 Azure AD를 구성할 수 있습니다. *다음 단계* 섹션에 대한 링크를 사용하여 자세한 내용을 알아보세요.

## <a name="next-steps"></a>다음 단계
- [Windows 10용 VDA 구성](/windows/deployment/vda-subscription-activation)에 대해 자세히 알아봅니다.
- [Windows 10용 다중 테넌트 호스팅](https://www.microsoft.com/en-us/CloudandHosting)에 대해 자세히 알아봅니다.
