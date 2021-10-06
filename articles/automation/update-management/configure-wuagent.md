---
title: Azure Automation 업데이트 관리를 위한 Windows 업데이트 설정 구성
description: 이 문서에서는 Azure Automation 업데이트 관리를 사용하도록 Windows 업데이트 설정을 구성하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 10/05/2021
ms.topic: conceptual
ms.openlocfilehash: 2d9d95c826af2d9448b296a69a815af26ab4fda4
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546663"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Azure Automation 업데이트 관리를 위한 Windows 업데이트 설정 구성

Azure Automation 업데이트 관리는 [Windows 업데이트 클라이언트](/windows/deployment/update/windows-update-overview)를 사용하여 Windows 업데이트를 다운로드하고 설치합니다. WSUS(Windows Server Update Services) 또는 Windows 업데이트에 연결할 때 Windows 업데이트 클라이언트에서 사용하는 특정 설정이 있습니다. 이러한 설정의 대부분은 다음을 통해 관리할 수 있습니다.

- 로컬 그룹 정책 편집기
- 그룹 정책
- PowerShell
- 레지스트리 직접 편집

업데이트 관리는 Windows 업데이트 클라이언트를 제어하기 위해 지정된 대부분의 설정을 따릅니다. 설정을 사용하여 비 Windows 업데이트를 사용하도록 설정하는 경우 업데이트 관리는 해당 업데이트도 관리합니다. 업데이트 배포를 수행하기 전에 업데이트 다운로드를 사용하도록 설정하면 업데이트 배포가 더 빠르고 효율적이며 유지 관리 기간을 초과할 가능성이 줄어듭니다.

Azure 구독에서 WSUS를 설정하고 Windows 가상 머신을 안전하게 최신 상태로 유지하는 다른 권장 사항은 [WSUS를 사용하여 Azure에서 Windows 가상 머신을 업데이트하기 위한 배포 계획을 검토하세요.](/azure/architecture/example-scenario/wsus/)

## <a name="pre-download-updates"></a>업데이트 사전 다운로드

업데이트를 자동으로 설치하지 않고 자동 다운로드를 구성하려면 그룹 정책 사용하여 자동 [업데이트 설정을 구성할](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)수 있습니다. 운영 체제 버전에 따라 두 가지 권장 값이 있습니다.

* Windows Server 2016 이상에서는 **를 7** 값으로 설정합니다.
* Windows Server 2012 R2 및 이전의 값은 **3으로** 설정되었습니다.

이 설정을 사용하면 백그라운드에서 필요한 업데이트를 다운로드할 수 있으며 업데이트를 설치할 준비가 되었음을 알려줍니다. 이러한 방식으로 업데이트 관리 일정을 제어할 수 있지만, 업데이트 관리 유지 관리 기간 이후에도 업데이트를 다운로드할 수 있습니다. 이 동작은 업데이트 관리에서 `Maintenance window exceeded` 오류를 방지합니다.

PowerShell에서 이 설정을 사용하도록 설정할 수 있습니다.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = <3 or 7>
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>재부팅 설정 구성

[레지스트리를 편집하여 자동 업데이트 구성](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) 및 [다시 시작 관리에 사용되는 레지스트리 키](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)에 나열된 레지스트리 키는 **업데이트 배포 설정** 에서 **다시 부팅 안 함** 을 지정한 경우에도 컴퓨터를 다시 부팅하게 만들 수 있습니다. 사용자 환경에 가장 적합하도록 이러한 레지스트리 키를 구성합니다.

## <a name="enable-updates-for-other-microsoft-products"></a>다른 Microsoft 제품에 대한 업데이트 사용

기본적으로 Windows 업데이트 클라이언트는 Windows에 대한 업데이트만 제공하도록 구성됩니다. **Windows를 업데이트할 때 다른 Microsoft 제품 업데이트 제공** 을 사용하도록 설정하면 Microsoft SQL Server 및 기타 Microsoft 소프트웨어에 대한 보안 패치를 비롯한 다른 제품의 업데이트도 받게 됩니다. Windows 2016 이상에서 사용할 수 있는 최신 [관리 템플릿 파일](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)을 다운로드하고 복사한 경우 이 옵션을 구성할 수 있습니다.

Windows Server 2012 R2를 실행하는 컴퓨터는 그룹 정책을 통해 이 설정을 구성할 수 없습니다. 해당 컴퓨터에서 다음 PowerShell 명령을 실행합니다.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>WSUS 구성 설정

업데이트 관리는 WSUS 설정을 지원합니다. [인트라넷 Microsoft 업데이트 서비스 위치 지정](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)의 지침을 사용하여 업데이트를 검색하고 다운로드하기 위한 소스를 지정할 수 있습니다. 기본적으로 Windows 업데이트 클라이언트는 Windows 업데이트에서 업데이트를 다운로드하도록 구성됩니다. WSUS 서버를 컴퓨터의 소스로 지정하는 경우 업데이트가 WSUS에서 승인되지 않으면 업데이트 배포가 실패합니다. 

컴퓨터를 내부 업데이트 서비스로 제한하려면 [모든 Windows 업데이트 인터넷 위치에 연결하지 마십시오](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)를 설정합니다.

## <a name="next-steps"></a>다음 단계

[Azure VM에 대한 업데이트 및 패치 관리](manage-updates-for-vm.md)의 지침에 따라 업데이트 배포를 예약합니다.
