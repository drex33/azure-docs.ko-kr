---
title: Azure 가상 머신 관련 보안 권장 사항
description: Azure에서 VM 관련 권장 사항을 적용하여 공유 책임 모델에 설명된 보안 책무를 달성하고 전반적인 배포 관련 보안 수준을 향상시킵니다.
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: afdd3928207dbe82afe1e55e16d9c2677741022d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345118"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Azure 가상 머신 관련 보안 권장 사항

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

이 문서에는 Azure Virtual Machines에 대한 보안 권장 사항이 포함되어 있습니다. 권장 사항을 따르면 공유 책임 모델에 설명된 보안 의무를 충족할 수 있습니다. 또한 권장 사항은 웹앱 솔루션의 전반적인 배포 관련 보안 수준을 높이는 데 도움이 됩니다. Microsoft에서 서비스 공급자 책임을 충족하기 위해 수행하는 작업에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)을 참조하세요.

이 문서의 권장 사항 중 일부는 Microsoft Defender for Cloud에서 자동으로 해결할 수 있습니다. Microsoft Defender for Cloud는 Azure의 리소스에 대한 첫 번째 방어선입니다. 이는 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 취약성을 식별합니다. 그런 다음 취약점을 해결하는 방법을 권장합니다. 자세한 내용은 [Microsoft Defender for Cloud의 보안 권장 사항을 참조하세요.](../security-center/security-center-recommendations.md)

클라우드용 Microsoft Defender에 대한 일반적인 내용은 Microsoft [Defender for Cloud란?을 참조하세요.](../security-center/security-center-introduction.md)

## <a name="general"></a>일반

| 권장 | 주석 | Defender for Cloud |
|-|----|--|
| 사용자 지정 VM 이미지를 빌드할 때 최신 업데이트를 적용합니다. | 이미지를 만들기 전에 이미지에 포함될 모든 애플리케이션 및 운영 체제에 대한 최신 업데이트를 설치합니다.  | - |
| 최신 상태로 VM 유지 | Azure Automation의 [업데이트 관리](../automation/update-management/overview.md) 솔루션을 사용하여 Azure에서 Windows 및 Linux 컴퓨터의 운영 체제 업데이트를 관리할 수 있습니다. | [예](../security-center/asset-inventory.md) |
| VM을 백업합니다. | [Azure Backup](../backup/backup-overview.md)은 애플리케이션 데이터를 보호하는 데 도움을 주며 운영 비용을 최소화합니다. 애플리케이션 오류로 인해 데이터가 손상되고 사용자 오류로 인해 애플리케이션에 버그가 발생할 수 있습니다. Azure Backup은 Windows 및 Linux를 실행하는 VM을 보호합니다. | - |
| 복원력 및 가용성 향상을 위해 여러 VM을 사용합니다. | 고가용성이어야 하는 애플리케이션을 VM이 실행하는 경우 여러 VM 또는 [가용성 집합](./availability.md)을 사용합니다. | - |
| BCDR(비즈니스 연속성 및 재해 복구) 전략을 채택합니다. | Azure Site Recovery를 사용하면 비즈니스 연속성을 지원하도록 설계된 다양한 옵션을 선택할 수 있습니다. 이는 다른 복제 및 장애 조치(failover) 시나리오를 지원합니다. 자세한 내용은 [Site Recovery 정보](../site-recovery/site-recovery-overview.md)를 참조하세요. | - |

## <a name="data-security"></a>데이터 보안

| 권장 | 주석 | Defender for Cloud |
|-|----|--|
| 운영 체제 디스크를 암호화합니다. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)을 사용하면 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있습니다. 필요한 키가 없으면 암호화된 디스크의 내용을 읽을 수 없습니다. 디스크 암호화는 디스크가 복사된 경우 저장된 데이터를 무단 액세스로부터 보호합니다.| [예](../security-center/asset-inventory.md) |
| 데이터 디스크를 암호화합니다. | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)을 사용하면 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있습니다. 필요한 키가 없으면 암호화된 디스크의 내용을 읽을 수 없습니다. 디스크 암호화는 디스크가 복사된 경우 저장된 데이터를 무단 액세스로부터 보호합니다.| -  |
| 설치된 소프트웨어를 제한합니다. | 설치된 소프트웨어를 솔루션을 성공적으로 적용하는 데 필요한 것으로 제한합니다. 이 지침을 통해 솔루션의 공격 노출 영역을 줄일 수 있습니다. | - |
| 바이러스 백신 또는 맬웨어 방지를 사용합니다. | Azure를 통해 Microsoft, Symantec, Trend Micro, Kaspersky 등 주요 보안 공급업체의 맬웨어 방지 소프트웨어를 사용할 수 있습니다. 이 소프트웨어는 악성 파일, 애드웨어 및 기타 위협으로부터 VM을 보호하도록 도와줍니다. 애플리케이션 워크로드에 따라 Microsoft Antimalware를 배포할 수 있습니다. Microsoft Antimalware는 Windows 머신에서만 사용할 수 있습니다. 보안 기본 설정 또는 고급 사용자 지정 구성을 사용합니다. 자세한 내용은 [Azure Cloud Services 및 Virtual Machines용 Microsoft Antimalware](../security/fundamentals/antimalware.md)를 참조하세요. | - |
| 안전하게 키 및 비밀을 저장합니다. | 애플리케이션 소유자에게 중앙에서 관리되는 보안 옵션을 제공하여 비밀 및 키 관리를 간소화합니다. 이 관리는 실수로 인한 손상 또는 유출의 위험을 줄여 줍니다. Azure Key Vault는 FIPS 140-2 Level 2에 따라 인증된 HSM(하드웨어 보안 모듈)에 키를 안전하게 저장할 수 있습니다. FIPS 140.2 Level 3을 사용하여 키와 비밀을 저장해야 하는 경우 [Azure Dedicated HSM](../dedicated-hsm/overview.md)을 사용할 수 있습니다. | - |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리 

| 권장 | 주석 | Defender for Cloud |
|-|----|--|
| VM 인증을 중앙 집중화합니다. | [Azure Active Directory 인증](../active-directory/develop/authentication-vs-authorization.md)을 사용하여 Windows 및 Linux VM의 인증을 중앙 집중화할 수 있습니다. | - |

## <a name="monitoring"></a>모니터링

| 권장 | 주석 | Defender for Cloud |
|-|----|--|
| VM을 모니터링합니다. | [VM용 Azure Monitor](../azure-monitor/vm/vminsights-overview.md)를 사용하여 Azure VM 및 Virtual Machine Scale Sets의 상태를 모니터링할 수 있습니다. VM의 성능 문제로 인해 가용성의 보안 원칙을 위반하는 서비스 중단이 발생할 수 있습니다. | - |

## <a name="networking"></a>네트워킹

| 권장 | 주석 | Defender for Cloud |
|-|----|--|
| 관리 포트에 대한 액세스를 제한합니다. | 공격자는 개방형 관리 포트의 퍼블릭 클라우드 IP 범위를 검색하고 공용 암호 및 패치가 적용되지 않은 알려진 취약점과 같은 “쉬운” 공격을 시도합니다. [JIT(Just-In-Time) VM 액세스](../security-center/security-center-just-in-time.md)를 사용하면 Azure VM에 대한 인바운드 트래픽을 잠가 공격에 대한 노출을 줄이는 동시에 VM에 쉽게 액세스하여 필요할 때 연결할 수 있습니다. | - |
| 네트워크 액세스를 제한합니다. | 네트워크 보안 그룹을 사용하면 네트워크 액세스를 제한하고 노출된 엔드포인트의 수를 제어할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 만들기, 변경 또는 삭제](../virtual-network/manage-network-security-group.md)를 참조하세요. | - |

## <a name="next-steps"></a>다음 단계

기타 보안 요구 사항을 알아보려면 애플리케이션 공급자에게 확인하세요. 보안 애플리케이션 개발에 대한 자세한 내용은 [보안 개발 설명서](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)를 참조하세요.
