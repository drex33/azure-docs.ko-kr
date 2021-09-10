---
title: 격리된 네트워크의 Azure Disk Encryption
description: 이 문서에서는 Linux VM의 Microsoft Azure Disk Encryption에 대한 문제 해결 팁을 알아봅니다.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 05/15/2021
ms.custom: seodec18
ms.openlocfilehash: fac7da74ee31f1bd775daa5446f4dec9b9bc2b79
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695660"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>격리된 네트워크의 Azure Disk Encryption

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

방화벽, 프록시 요구 사항 또는 NSG(네트워크 보안 그룹) 설정으로 연결이 제한되면 필요한 작업을 수행할 수 있는 확장의 기능이 중단될 수 있습니다. 이로 인해 "VM에서 사용할 수 없는 확장 상태"와 같은 상태 메시지가 표시됩니다.

## <a name="package-management"></a>패키지 관리

Azure Disk Encryption은 일반적으로 ADE를 사용할 때 설치되는 구성 요소 수에 따라 달라집니다. 방화벽 뒤에 있거나 인터넷에서 격리된 경우에는 관련 패키지를 미리 설치하거나 로컬로 사용할 수 있어야 합니다.

각 배포에 필요한 패키지는 다음과 같습니다. 지원되는 배포판과 볼륨 유형의 전체 목록은 [지원되는 VM 및 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)를 참조하세요.

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps, grub-pc-bin
- **CentOS 7.2 - 7.9, 8.1, 8.2**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.9, 8.1, 8.2**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

Red Hat에서 프록시가 필요한 경우 subscription-manager와 yum이 올바르게 설정되었는지 확인해야 합니다. 자세한 내용은 [구독 관리자 및 yum 문제를 해결하는 방법](https://access.redhat.com/solutions/189533)을 참조하세요.  

패키지를 수동으로 설치하는 경우 새 버전이 릴리스되면 수동으로 업그레이드해야 합니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹
적용되는 모든 네트워크 보안 그룹 설정은 엔드포인트에서도 디스크 암호화에 대해 문서화된 네트워크 구성 필수 조건을 충족해야 합니다.  [Azure Disk Encryption: 네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements)을 참조하세요.

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption과 Azure AD(이전 버전)

[Azure Disk Encryption과 Azure AD(이전 버전)](disk-encryption-overview-aad.md)를 함께 사용하는 경우 [위 목록](#package-management)에 나열된 것처럼 배포에 적합한 패키지 외에도 모든 배포에 대해 [Azure Active Directory 라이브러리](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)를 수동으로 설치해야 합니다.

[Azure AD 자격 증명](disk-encryption-linux-aad.md)에 암호화가 사용하도록 설정된 경우 대상 VM은 Azure Active Directory 엔드포인트 및 Key Vault 엔드포인트 모두에 대한 연결을 허용해야 합니다. 현재 Azure Active Directory 인증 엔드포인트는 [Microsoft 365 URL 및 IP 주소 범위](/microsoft-365/enterprise/urls-and-ip-address-ranges) 설명서의 56 및 59 섹션에서 유지 관리됩니다. Key Vault 지침은 [방화벽 뒤에 있는 Azure Key Vault에 액세스](../../key-vault/general/access-behind-firewall.md)하는 방법에 관한 설명서에서 제공됩니다.

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 

가상 머신은 VM 내에서만 액세스할 수 있는 잘 알려진 라우팅이 불가능한 IP 주소(`169.254.169.254`)를 사용하는 [Azure Instance Metadata 서비스](instance-metadata-service.md) 엔드포인트에 액세스할 수 있어야 합니다.  이 주소에 대한 로컬 HTTP 트래픽(예: X-Forwarded-For 헤더)을 변경하는 프록시 구성은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)에 대한 추가 단계 참조
- [휴지 상태의 Azure 데이터 암호화](../../security/fundamentals/encryption-atrest.md)
