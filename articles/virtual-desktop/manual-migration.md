---
title: Azure Virtual Desktop(클래식)에서 수동으로 마이그레이션 - Azure
description: Azure Virtual Desktop(클래식)에서 Azure Virtual Desktop으로 수동으로 마이그레이션하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: aa802843f76f2707d2df1d9018b60a1e8090cfb5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438807"
---
# <a name="migrate-manually-from-azure-virtual-desktop-classic"></a>Azure Virtual Desktop(클래식)에서 수동으로 마이그레이션

Azure Virtual Desktop(클래식)은 PowerShell cmdlet, REST API 및 서비스 개체를 사용하여 서비스 환경을 만듭니다. Azure Virtual Desktop 서비스 환경의 "개체"는 Azure Virtual Desktop에서 만드는 작업입니다. 서비스 개체에는 테넌트, 호스트 풀, 애플리케이션 그룹 및 세션 호스트가 포함됩니다.

그러나 Azure Virtual Desktop(클래식)은 Azure와 통합되지 않습니다. Azure가 통합되지 않은 경우 사용자가 만드는 모든 개체는 Azure 구독에 연결되어 있지 않기 때문에 Azure Portal에서 자동으로 관리되지 않습니다.

Azure Virtual Desktop의 최신 주요 업데이트는 서비스의 변화를 전체 Azure 통합으로 표시합니다. Azure Virtual Desktop에서 만든 개체는 Azure Portal에 의해 자동으로 관리됩니다.

이 문서에서는 Azure Virtual Desktop의 최신 버전으로 마이그레이션하는 것을 고려해야 하는 이유를 설명합니다. 그런 다음, Azure Virtual Desktop(클래식)에서 Azure Virtual Desktop의 최신 업데이트로 수동으로 마이그레이션하는 방법을 설명합니다.

## <a name="why-migrate"></a>마이그레이션해야 하는 이유는 무엇인가요?

특히 수동으로 수행해야 하는 주요 업데이트는 불편할 수 있습니다. 그러나 자동으로 마이그레이션할 수 없는 몇 가지 이유는 다음과 같습니다.

- 클래식 릴리스를 사용하여 만든 기존 서비스 개체는 Azure에 표시되지 않습니다. 해당 범위는 Azure Virtual Desktop 서비스를 넘어 확장되지 않습니다.
- 최신 업데이트를 사용하여 서비스의 애플리케이션 ID가 Azure Virtual Desktop(클래식)에 대해 수행한 방식으로 앱에 대한 동의를 제거하도록 변경되었습니다. 새 애플리케이션 ID로 인증되지 않은 경우 Azure Virtual Desktop을 사용하여 새 Azure 개체를 만들 수 없습니다.

문제가 있더라도 클래식 버전에서 마이그레이션하는 것은 여전히 중요합니다. 마이그레이션한 후에 수행할 수 있는 작업은 다음과 같습니다.

- Azure Portal을 통해 Azure Virtual Desktop을 관리합니다.
- 애플리케이션 그룹에 Azure AD(Active Directory) 사용자 그룹을 할당합니다.
- 향상된 Log Analytics 기능을 사용하여 배포 문제를 해결합니다.
- Azure RBAC(Azure 기본 역할 기반 액세스 제어)를 사용하여 관리 액세스를 관리합니다.

## <a name="when-should-i-migrate"></a>언제 마이그레이션해야 하나요?

사용자에게 마이그레이션해야 하는지 여부를 확인하는 경우 배포의 현재 및 향후 상황을 고려해야 합니다.

특히 수동으로 마이그레이션하는 것이 권장되는 시나리오는 다음과 같습니다.

- 적은 수의 사용자로 테스트 호스트 풀을 설정했습니다.
- 적은 수의 사용자를 포함하는 프로덕션 호스트 풀을 설정하는 경우 최종적으로는 수백 명의 사용자가 될 계획입니다.
- 쉽게 복제할 수 있는 간단한 설정이 있습니다. 예를 들어 VM에서 갤러리 이미지를 사용하는 경우입니다.

> [!IMPORTANT]
> 안정화하는 데 오랜 시간이 걸리거나 많은 사용자가 있는 고급 구성을 사용하는 경우 수동으로 마이그레이션하지 않는 것이 좋습니다.

## <a name="prepare-for-migration"></a>마이그레이션 준비

시작하기 전에 환경을 마이그레이션할 준비가 되었는지 확인해야 합니다.

마이그레이션 프로세스를 시작하는 데 필요한 사항은 다음과 같습니다.

- Azure 구독 - 새 Azure 서비스 개체를 만듭니다.
- 다음 역할에 할당되었는지 확인합니다.
    
    - 참가자
    - 사용자 액세스 관리자
    
    기여자 역할을 통해 구독에서 Azure 개체를 만들 수 있으며, 사용자 액세스 관리자 역할을 통해 애플리케이션 그룹에 사용자를 할당할 수 있습니다.

## <a name="how-to-migrate-manually"></a>수동으로 마이그레이션하는 방법

마이그레이션 프로세스를 준비한 후에는 실제로 마이그레이션해야 합니다.

Azure Virtual Desktop(클래식)에서 Azure Virtual Desktop으로 수동으로 마이그레이션하려면 다음을 수행합니다.

1. [Azure Portal을 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)의 지침을 따라 Azure Portal을 사용하여 모든 상위 수준 개체를 만듭니다.
2. 이미 사용하고 있는 가상 머신을 가져오려면 [Azure Virtual Desktop 호스트 풀에 가상 머신 등록](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool)의 지침에 따라 가상 머신을 1단계에서 만든 새 호스트 풀에 수동으로 등록합니다.
3. 새 RemoteApp 앱 그룹을 만듭니다.
4. 사용자 또는 사용자 그룹을 새 데스크톱 및 RemoteApp 앱 그룹에 게시합니다.
5. [다단계 인증 설정](set-up-mfa.md)의 지침에 따라 새 개체를 허용하도록 조건부 액세스 정책을 업데이트합니다.

가동 중지 시간을 방지하려면 먼저 기존 세션 호스트를 작은 그룹의 Azure Resource Manager 통합 호스트 풀에 한 번에 등록해야 합니다. 그런 다음, 사용자를 천천히 새 Azure Resource Manager 통합 앱 그룹으로 전환합니다.

## <a name="next-steps"></a>다음 단계

배포를 자동으로 마이그레이션하는 방법을 알아보려면 [Azure 가상 데스크톱에서 자동으로 마이그레이션 (클래식)](automatic-migration.md)을 참조 하세요.

마이그레이션한 후에 [자습서](create-host-pools-azure-marketplace.md)를 확인하여 Azure Virtual Desktop이 어떻게 작동하는지 알아봅니다. [기존 호스트 풀 확장](expand-existing-host-pool.md) 및 [RDP 속성 사용자 지정](customize-rdp-properties.md)에서 고급 관리 기능에 대해 알아봅니다.

서비스 개체에 대해 자세히 알아보려면 [Azure Virtual Desktop 환경](environment-setup.md)을 확인합니다.
