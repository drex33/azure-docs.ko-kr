---
title: Azure Virtual Desktop(클래식) 호스트 풀 Azure Resource Manager - Azure
description: Azure Resource Manager 템플릿을 사용하여 Azure Virtual Desktop(클래식)에서 호스트 풀을 만드는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: efc8c93c93325e41c1f8fd9c22baf4c036012ab6
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749924"
---
# <a name="create-a-host-pool-in-azure-virtual-desktop-classic-with-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Azure Virtual Desktop(클래식)에서 호스트 풀을 만듭니다.

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다.

호스트 풀은 Azure Virtual Desktop 테넌트 환경 내에서 하나 이상의 동일한 가상 머신 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다.

이 섹션의 지침에 따라 Microsoft에서 제공하는 Azure Resource Manager 템플릿을 사용하여 Azure Virtual Desktop 테넌트에 대한 호스트 풀을 만듭니다. 이 문서에서는 Azure Virtual Desktop에서 호스트 풀을 만들고, Azure 구독에서 VM을 사용하여 리소스 그룹을 만들고, 해당 VM을 AD 도메인에 조인하고, VM을 Azure Virtual Desktop에 등록하는 방법에 대해 설명합니다.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 실행하기 위해 필요한 것

Azure Resource Manager 템플릿을 실행하기 전에 다음 사항을 알고 있어야 합니다.

- 사용하려는 이미지의 원본이 있는 위치. Azure 갤러리에서 가져온 것인가요, 아니면 사용자 지정인가요?
- 도메인 조인 자격 증명
- Azure Virtual Desktop 자격 증명

Azure Resource Manager 템플릿을 사용하여 Azure Virtual Desktop 호스트 풀을 만드는 경우 Azure 갤러리, 관리형 이미지 또는 관리되지 않는 이미지에서 가상 머신을 만들 수 있습니다. VM 이미지를 만드는 방법에 대한 자세한 내용은 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](../../virtual-machines/windows/prepare-for-upload-vhd-image.md) 및 [Azure에서 일반화된 VM의 관리 이미지 만들기](../../virtual-machines/windows/capture-image-resource.md)를 참조하세요.

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Azure Resource Manager 템플릿을 실행하여 새 호스트 풀 프로비저닝

시작하려면 [이 GitHub URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)로 이동합니다.

### <a name="deploy-the-template-to-azure"></a>Azure에 템플릿 배포

엔터프라이즈 구독에 배포하는 경우 아래로 스크롤하여 **Azure에 배포** 를 선택한 다음, 이미지 원본에 따라 매개 변수를 채웁니다.

클라우드 솔루션 공급자 구독에 배포하는 경우 다음 단계에 따라 Azure에 배포합니다.

1. 아래로 스크롤하여 **Azure에 배포** 를 마우스 오른쪽 단추로 클릭한 다음, **링크 위치 복사** 를 선택합니다.
2. 메모장 같은 텍스트 편집기를 열고 링크를 붙여넣습니다.
3. "https://portal.azure.com/" 바로 뒤에서 해시 태그(#) 앞에 기호(@)와 테넌트 도메인 이름을 차례로 입력합니다. `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`는 사용해야 하는 형식의 예입니다.
4. 클라우드 솔루션 공급자 구독에 대한 관리자/기여자 권한이 있는 사용자로 Azure Portal에 로그인합니다.
5. 텍스트 편집기에 복사한 링크를 주소 표시줄에 붙여넣습니다.

시나리오에 입력해야 하는 매개 변수에 대한 지침은 Azure Virtual Desktop [추가 정보 파일](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)을 참조하세요. 추가 정보는 항상 최신 변경 내용으로 업데이트됩니다.

## <a name="assign-users-to-the-desktop-application-group"></a>데스크톱 애플리케이션 그룹에 사용자 할당

GitHub Azure Resource Manager 템플릿이 완료되면 가상 머신에서 전체 세션 데스크톱 테스트를 시작하기 전에 사용자 액세스 권한을 할당합니다.

먼저, 아직 수행하지 않은 경우 PowerShell 세션에서 사용할 [Azure Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/).

데스크톱 애플리케이션 그룹에 사용자를 할당하려면 PowerShell 창을 열고 이 cmdlet을 실행하여 Azure Virtual Desktop 환경에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

그런 다음, 이 cmdlet을 사용하여 데스크톱 애플리케이션 그룹에 사용자를 추가합니다.

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

사용자의 UPN은 Azure Active Directory의 사용자 ID와 일치해야 합니다(예: user1@contoso.com). 여러 사용자를 추가하려는 경우 각 사용자에 대해 이 cmdlet을 실행해야 합니다.

이러한 단계를 완료하면 데스크톱 애플리케이션 그룹에 추가된 사용자가 지원되는 원격 데스크톱 클라이언트를 사용하여 Azure Virtual Desktop에 로그인하고 세션 데스크톱에 대한 리소스를 확인할 수 있습니다.

>[!IMPORTANT]
>Azure에서 Azure Virtual Desktop 환경의 보안을 유지하기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Azure Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](../../security-center/security-center-just-in-time.md)를 사용하는 것이 좋습니다.