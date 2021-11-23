---
title: Azure 가상 데스크톱 (클래식) 삭제-Azure
description: Azure 가상 데스크톱 (클래식)을 더 이상 사용 하지 않는 경우 정리 하는 방법입니다.
author: jensheerin
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jensheerin
manager: annathan
ms.openlocfilehash: 5b1bef391990c2109fed3d5b9b5d5bbd9cf8da75
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132943467"
---
# <a name="delete-azure-virtual-desktop-classic"></a>Azure 가상 데스크톱 (클래식) 삭제
이 문서에서는 Azure 가상 데스크톱 (클래식)을 삭제 하는 방법을 설명 합니다.

> [!WARNING]
> 이 문서에 설명된 대로 리소스를 삭제하면 이러한 작업은 되돌릴 수 없습니다.


## <a name="requirements"></a>요구 사항

시작 하기 전에 다음 항목이 준비 되었는지 확인 합니다.

- Azure Active Directory 테 넌 트 내의 전역 관리자 계정

- 아직 없는 경우 PowerShell 세션에서 사용할 [Azure 가상 데스크톱 모듈을 다운로드 하 여 가져옵니다](/powershell/windows-virtual-desktop/overview/) .

## <a name="delete-azure-virtual-desktop-classic"></a>Azure 가상 데스크톱 (클래식) 삭제
1. PowerShell 창에서 Windows 가상 데스크톱 (클래식)에 로그인 합니다.

    ```powershell
    Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
    ```

2. Azure Resource Manager에 로그인 합니다.

    ```powershell
    Login-AzAccount
    ```

3. 여러 구독이 있는 경우이 cmdlet을 사용 하 여 리소스를 삭제 하려는 구독을 선택 합니다.

    ```powershell
    Select-AzSubscription -Subscriptionid <subID>
    ```

4. Get 명령을 실행 하 여 RDS 리소스 찾기
    ```Get
    Get-RDSTenant
    Get-RDSHostPool -TenantName <tenantname>
    Get-RDSSessionHost -TenantName <tenantname> -HostPoolName <hostpoolname>
    Get-RDSAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname>
    Get-RDSRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
    ```

5.  다음 단계는 Azure 가상 데스크톱의 클래식 버전을 제거 합니다.
    ```Remove
    Remove-RDSRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -name 'name'
    Remove-RDSAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname> -Name <appgroupname>
    Remove-RDSSessionHost -TenantName <tenantname> -HostPoolName <hostpoolname> -Name <sessionhostname>
    Remove-RDSHostPool -TenantName <tenantname> -Name <hostpoolname>
    Remove-RDSTenant -Name <tenantname>
    ```