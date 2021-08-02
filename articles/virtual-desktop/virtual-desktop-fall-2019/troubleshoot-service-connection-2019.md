---
title: 서비스 연결 문제 해결 Azure Virtual Desktop(클래식) - Azure
description: Azure Virtual Desktop(클래식) 테넌트 환경에서 클라이언트 연결을 설정할 때 발생하는 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 856d6e65273b15c843bffe40c818b07c60f00fb0
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753938"
---
# <a name="azure-virtual-desktop-classic-service-connections"></a>Azure Virtual Desktop(클래식) 서비스 연결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../troubleshoot-service-connection.md)를 참조하세요.

이 문서를 사용하여 Azure Virtual Desktop 클라이언트 연결 문제를 해결합니다.

## <a name="provide-feedback"></a>피드백 제공

[Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 제품 팀 및 기타 활성 커뮤니티 멤버와 Azure Virtual Desktop Service에 대해 피드백을 제공하고 논의할 수 있습니다.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>사용자가 연결했지만 아무것도 표시되지 않음(피드 없음)

사용자는 원격 데스크톱 클라이언트를 시작하고 인증할 수 있지만 웹 검색 피드에 아이콘이 표시되지 않습니다.

다음 명령줄을 사용하여 문제를 보고하는 사용자가 애플리케이션 그룹에 할당되었는지 확인합니다.

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

사용자가 올바른 자격 증명을 사용하여 로그인하고 있는지 확인합니다.

웹 클라이언트를 사용하는 경우 캐시된 자격 증명 문제가 없는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop과 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview-2019.md)을 참조하세요.
- Azure Virtual Desktop 환경에서 테넌트와 호스트 풀을 만드는 동안 발생하는 문제를 해결하려면 [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues-2019.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration-2019.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
