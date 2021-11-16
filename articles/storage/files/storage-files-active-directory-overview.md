---
title: 개요 - Azure Files ID 기반 권한 부여
description: Azure Files는 AD DS(Azure Active Directory Domain Services) 및 Active Directory를 사용하여 SMB(서버 메시지 블록)를 통한 ID 기반 인증을 지원합니다. 도메인 가입 Windows VM(가상 머신)은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 43e844380374a8e507325494b67faef1daf85340
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523945"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>SMB 액세스에 대한 Azure Files ID 기반 인증 옵션 개요
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure 파일 공유에 대해 온-프레미스 Active Directory Domain Services 인증을 사용하도록 설정하는 방법을 알아보려면 [Azure 파일 공유에 대해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-enable.md)을 참조하세요.

Azure 파일 공유에 대해 Azure AD DS 인증을 사용하는 방법을 알아보려면 [Azure Files에서 Azure Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-domain-service-enable.md)을 참조하세요.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="glossary"></a>용어 
Azure 파일 공유에 대해 SMB를 통한 Azure AD Domain Service 인증과 관련된 몇 가지 주요 용어를 알고 있는 것이 좋습니다.

-   **Kerberos 인증**

    Kerberos는 사용자 또는 호스트의 ID를 확인하는 데 사용되는 인증 프로토콜입니다. Kerberos에 대한 자세한 내용은 [Kerberos 인증 개요](/windows-server/security/kerberos/kerberos-authentication-overview)를 참조하세요.

-  **SMB(서버 메시지 블록) 프로토콜**

    SMB는 산업 표준 네트워크 파일 공유 프로토콜입니다. SMB는 CIFS(Common Internet File System)라고도 합니다. SMB에 대한 자세한 내용은 [Microsoft SMB Protocol and CIFS Protocol Overview](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)(Microsoft SMB 프로토콜 및 CIFS 프로토콜 개요)를 참조하세요.

-   **Azure AD(Azure Active Directory)**

    Azure AD(Azure Active Directory)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure AD에서는 핵심 디렉터리 서비스, 애플리케이션 액세스 관리 및 ID 보호가 하나의 솔루션으로 결합되어 있습니다. Azure AD 가입 Windows VM(가상 머신)은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 없습니다. 자세한 내용은 [Azure Active Directory란?](../../active-directory/fundamentals/active-directory-whatis.md)을 참조하세요.

-   **Azure AD DS(Azure Active Directory Domain Services)**

    Azure AD DS는 도메인 가입, 그룹 정책, LDAP, Kerberos/NTLM 인증 등의 관리되는 도메인 서비스를 제공합니다. 이러한 서비스는 Active Directory Domain Services와 완벽하게 호환됩니다. 자세한 내용은 [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)를 참조하세요.

- **온-프레미스 AD DS(Active Directory Domain Services)**

    Azure Files와 통합된 온-프레미스 AD DS(Active Directory Domain Services)는 네트워크 사용자 및 관리자가 디렉터리 데이터를 사용할 수 있도록 하면서 디렉터리 데이터를 저장하는 방법을 제공합니다. 보안은 디렉터리의 개체에 대한 로그온 인증과 액세스 제어를 통해 AD DS와 통합됩니다. 관리자는 단일 네트워크 로그온을 통해 네트워크에서 디렉터리 데이터 및 조직을 관리할 수 있으며, 권한 있는 네트워크 사용자는 네트워크의 모든 위치에서 리소스에 액세스할 수 있습니다. AD DS는 일반적으로 온-프레미스 환경에서 기업에 의해 채택되며 AD DS 자격 증명이 액세스 제어를 위한 ID로 사용됩니다. 자세한 내용은 [Active Directory Domain Services 개요](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)를 참조하세요.

-   **Azure RBAC(Azure 역할 기반 액세스 제어)**

    Azure RBAC(역할 기반 액세스 제어)를 사용하면 Azure에 대한 세분화된 액세스 관리가 가능합니다. Azure RBAC를 사용하면 사용자에게 작업을 수행하는 데 필요한 최소한의 권한을 부여하여 리소스에 대한 액세스를 관리할 수 있습니다. Azure RBAC에 대한 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)을 참조하세요.

## <a name="common-use-cases"></a>일반 사용 예

Azure Files에서 Windows ACL에 대한 ID 기반 인증 및 지원은 다음과 같은 사용 사례에서 가장 잘 활용됩니다.

### <a name="replace-on-premises-file-servers"></a>온-프레미스 파일 서버 바꾸기

분산된 온-프레미스 파일 서버를 사용 중단하고 교체하는 것은 모든 기업이 IT 현대화 과정에서 경험하는 일반적인 문제입니다. 온-프레미스 AD DS 인증을 사용하는 Azure 파일 공유는 데이터를 Azure Files로 마이그레이션할 수 있는 경우에 가장 적합합니다. 완전히 마이그레이션하면 클라이언트 쪽 변경을 최소화하면서 고가용성 및 확장성 이점을 최대한 활용할 수 있습니다. 최종 사용자에게 원활한 마이그레이션 환경을 제공하므로 기존 도메인 가입 컴퓨터를 사용하여 동일한 자격 증명으로 데이터에 계속 액세스할 수 있습니다.

### <a name="lift-and-shift-applications-to-azure"></a>Azure로 애플리케이션 리프트 앤 시프트

애플리케이션을 클라우드로 리프트 앤 시프트하는 경우 데이터에 대해 동일한 인증 모델을 유지하려고 할 수 있습니다. ID 기반 액세스 제어 환경을 Azure 파일 공유로 확장하는 것이므로 애플리케이션을 최신 인증 방법으로 변경하고 클라우드 채택을 신속하게 처리할 필요가 없습니다. Azure 파일 공유에서는 인증을 위해 Azure AD DS 또는 온-프레미스 AD DS와 통합하는 옵션을 제공합니다. 100% 클라우드 네이티브이고 클라우드 인프라를 관리하는 비용을 최소화하려는 계획일 경우 Azure AD DS가 완전 관리형 도메인 서비스로 더 적합합니다. AD DS 기능과의 완전한 호환성이 필요한 경우에는 VM에서 도메인 컨트롤러를 자체 호스팅하여 AD DS 환경을 클라우드로 확장할 수도 있습니다. 어느 방법을 사용하든 비즈니스 요구 사항에 맞는 도메인 서비스를 유연하게 선택할 수 있습니다.

### <a name="backup-and-disaster-recovery-dr"></a>백업 및 재해 복구(DR)

기본 파일 스토리지를 온-프레미스에 유지하는 경우 비즈니스 연속성을 개선하기 위해 백업 또는 DR에 적합한 스토리지로 Azure 파일 공유를 사용할 수 있습니다. Windows DACL을 유지하면서 Azure 파일 공유를 사용하여 기존 파일 서버에서 데이터를 백업할 수 있습니다. DR 시나리오의 경우 장애 조치(failover) 시 적절한 액세스 제어 적용을 지원하도록 인증 옵션을 구성할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

다음 표에는 Azure AD DS 및 온-프레미스 AD DS에 대해 지원되는 Azure 파일 공유 인증 시나리오가 요약되어 있습니다. Azure Files와 통합하기 위해 클라이언트 환경에 채택한 도메인 서비스를 선택하는 것이 좋습니다. 디바이스가 AD에 도메인에 가입된 온-프레미스 또는 Azure에 이미 AD DS가 있는 경우 Azure 파일 공유 인증에 AD DS를 사용하도록 선택해야 합니다. 마찬가지로 Azure AD DS를 이미 채택한 경우에는 Azure 파일 공유에 인증하는 데도 사용해야 합니다.


|Azure AD DS 인증  | 온-프레미스 AD DS 인증  |
|---------|---------|
|Azure AD DS 조인된 Windows 머신은 SMB를 통해 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.     |온-프레미스 AD DS 조인 또는 Azure AD DS 조인된 Windows 머신은 SMB를 통해 Azure AD에 동기화된 온-프레미스 Active Directory 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다. 클라이언트가 AD DS에 연결되어 있어야 합니다.        |

### <a name="restrictions"></a>제한

- Azure AD DS 및 온-프레미스 AD DS 인증은 컴퓨터 계정에 대한 인증을 지원하지 않습니다. 대신 서비스 로그온 계정을 사용하는 것이 좋습니다.
- Azure AD DS 인증 또는 온-프레미스 AD DS 인증은 Azure AD 조인 디바이스 또는 Azure AD 등록 디바이스에 대해 지원되지 않습니다.
- Azure 파일 공유는 [Azure AD DS(Azure Active Directory Domain Services)](#azure-ad-ds) 또는 [온-프레미스 AD DS(Active Directory Domain Services)](#ad-ds) 도메인 서비스 중 하나에 대한 ID 기반 인증만 지원합니다.
- NFS (네트워크 파일 시스템) 공유에서는 id 기반 인증 방법이 지원 되지 않습니다.

## <a name="advantages-of-identity-based-authentication"></a>ID 기반 인증의 이점
Azure Files에 대한 ID 기반 인증은 공유 키 인증을 사용하는 경우에 비해 다음과 같은 여러 이점을 제공합니다.

-   **온-프레미스 AD DS 및 Azure AD DS를 통해 기존 ID 기반 파일 공유 액세스 환경을 클라우드로 확장**  
    애플리케이션을 클라우드로 리프트 앤 시프트하여 기존 파일 서버를 Azure 파일 공유로 바꿀 계획인 경우, 애플리케이션이 온-프레미스 AD DS 또는 Azure AD DS 자격 증명 중 하나로 인증하여 파일 데이터에 액세스하기를 원할 수 있습니다. Azure Files는 온-프레미스 AD DS 또는 Azure AD DS 자격 증명을 모두 사용하여 온-프레미스 AD DS 또는 Azure AD DS 도메인 중 하나에 가입된 VM에서 SMB를 통해 Azure 파일 공유에 액세스할 수 있도록 지원합니다.

-   **Azure 파일 공유에 대한 세부적인 액세스 제어 적용**  
    공유, 디렉터리 또는 파일 수준에서 특정 ID에 사용 권한을 부여할 수 있습니다. 예를 들어 프로젝트 협업을 위해 단일 Azure 파일 공유를 사용하는 여러 팀이 있다고 가정할 수 있습니다. 이런 경우 중요하지 않은 디렉터리에 대한 액세스 권한은 모든 팀에 부여하면서 중요한 재무 데이터가 포함된 디렉터리에 대한 액세스 권한은 재무 팀으로만 제한할 수 있습니다. 

-   **데이터와 함께 Windows ACL(NTFS라고도 함) 백업**  
    Azure 파일 공유를 사용하여 기존 온-프레미스 파일 공유를 백업할 수 있습니다. SMB를 통해 Azure 파일 공유로 파일 공유를 백업하는 경우 Azure Files에서는 데이터와 함께 ACL이 유지됩니다.

## <a name="how-it-works"></a>작동 방식

Azure 파일 공유는 온-프레미스 AD DS 또는 Azure AD DS 중 하나로 인증하는 데 Kerberos 프로토콜을 활용합니다. 클라이언트에서 실행되는 사용자 또는 애플리케이션과 연결된 ID가 Azure 파일 공유의 데이터에 액세스하려고 하면 ID를 인증하기 위해 AD DS 또는 Azure AD DS 도메인 서비스 중 하나로 요청이 전송됩니다. 인증에 성공하면 Kerberos 토큰이 반환됩니다. 클라이언트에서 Kerberos 토큰이 포함된 요청을 보내면 Azure 파일 공유에서 해당 토큰을 사용하여 요청에 권한을 부여합니다. Azure 파일 공유는 자격 증명에 액세스하지 않고 Kerberos 토큰만 수신합니다.

Azure 파일 공유에서 ID 기반 인증을 사용하도록 설정하려면 먼저 도메인 환경을 설정해야 합니다.

### <a name="ad-ds"></a>AD DS

온-프레미스 AD DS 인증의 경우 AD 도메인 컨트롤러를 설정하고 컴퓨터 또는 VM에 도메인 가입을 설정해야 합니다. Azure VM 또는 온-프레미스에서 도메인 컨트롤러를 호스트할 수 있습니다. 어느 쪽이든 도메인에 가입된 클라이언트는 도메인 서비스에 바로 연결되어야 하므로 도메인 서비스의 회사 네트워크 또는 VNET(가상 네트워크) 내에 있어야 합니다.

다음 다이어그램은 SMB를 통해 Azure 파일 공유에 대한 온-프레미스 AD DS 인증을 보여 줍니다. 온-프레미스 AD DS는 Azure AD Connect 동기화를 사용하여 Azure AD에 동기화해야 합니다. 온-프레미스 AD DS 및 Azure AD 둘 모두에 존재하는 하이브리드 사용자만 인증하고 Azure 파일 공유에 대한 액세스 권한을 부여할 수 있습니다. 이는 AD DS에서 디렉터리/파일 수준 사용 권한이 적용되는 Azure AD에 표시된 ID에 대해 공유 수준 권한이 구성되기 때문입니다. 동일한 하이브리드 사용자에 대해 사용 권한을 올바르게 구성해야 합니다.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="SMB를 통해 Azure 파일 공유에 대한 온-프레미스 AD DS 인증을 보여 주는 다이어그램":::

### <a name="azure-ad-ds"></a>Azure AD DS

Azure AD DS 인증의 경우 파일 데이터에 액세스하려는 VM에 대한 Azure AD Domain Services 및 도메인 가입을 사용하도록 설정해야 합니다. 도메인 가입 VM은 Azure AD DS와 동일한 VNET(가상 네트워크)에 있어야 합니다. 

다음 다이어그램은 SMB를 통해 Azure 파일 공유에 대해 Azure AD DS를 인증하는 워크플로를 나타냅니다. Azure 파일 공유에 대한 온-프레미스 AD DS 인증과 비슷한 패턴을 따릅니다. 다음과 같은 두 가지 주요 차이점이 있습니다.

- 먼저 스토리지 계정을 나타내기 위해 사용자가 Azure AD DS에서 ID를 만들 필요가 없습니다. 이 작업은 백그라운드에서 활성화 프로세스를 통해 수행됩니다.

- 둘째, Azure AD에 있는 모든 사용자를 인증하고 권한을 부여할 수 있습니다. 사용자는 클라우드 전용이거나 하이브리드일 수 있습니다. Azure AD에서 Azure AD DS로의 동기화는 플랫폼에 의해 관리되며 사용자 구성이 필요하지 않습니다. 그러나 클라이언트는 Azure AD DS에 가입된 도메인이어야 하며, Azure AD에 조인되거나 등록되어서는 안됩니다. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="다이어그램":::

### <a name="enable-identity-based-authentication"></a>ID 기반 인증 사용

새로운 스토리지 계정과 기존 스토리지 계정에서 Azure 파일 공유에 대해 Azure AD DS 또는 온-프레미스 AD DS 중 하나를 사용하여 ID 기반 인증을 사용하도록 설정할 수 있습니다. 스토리지 계정의 파일 액세스 인증에는 하나의 도메인 서비스만 사용할 수 있으며, 이는 계정의 모든 파일 공유에 적용됩니다. Azure AD DS를 사용해 인증하도록 파일 공유를 설정하는 방법에 대한 자세한 내용은 [Azure Files에서 Azure Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-domain-service-enable.md) 문서를 참조하고 온-프레미스 AD DS에 대한 지침은 [Azure 파일 공유에 대해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-enable.md) 문서를 참조하세요.

### <a name="configure-share-level-permissions-for-azure-files"></a>Azure Files에 대한 공유 수준 권한 구성

Azure AD DS 또는 온-프레미스 AD DS 인증 중 하나를 사용하도록 설정하면 Azure 기본 제공 역할을 사용하거나, Azure AD ID에 대한 사용자 지정 역할을 구성하고 스토리지 계정의 모든 파일 공유에 액세스 권한을 할당할 수 있습니다. 할당된 사용 권한을 사용하면 권한이 부여된 ID에서 공유만 액세스할 수 있으며 그 외에는 루트 디렉터리도 액세스할 수 없습니다. Azure 파일 공유에 대한 디렉터리 수준 또는 파일 수준 권한은 별도로 구성해야 합니다.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Azure Files에 대한 디렉터리 또는 파일 수준 권한 구성

Azure 파일 공유는 루트 디렉터리를 포함하여 디렉터리와 파일 수준 모두에 표준 Windows 파일 권한을 적용합니다. 디렉터리 또는 파일 수준 권한 구성은 SMB와 REST를 통해서 지원됩니다. VM에서 대상 파일 공유를 탑재하고 Windows 파일 탐색기, Windows [icacls](/windows-server/administration/windows-commands/icacls) 또는 [Set-ACL](/powershell/module/microsoft.powershell.security/get-acl) 명령을 사용하여 권한을 구성합니다.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>슈퍼 사용자 권한은 스토리지 계정 키 사용

스토리지 계정 키를 보유한 사용자는 슈퍼 사용자 권한으로 Azure 파일 공유에 액세스할 수 있습니다. 슈퍼 사용자 권한은 모든 액세스 제어 제한 사항을 무시합니다.

> [!IMPORTANT]
> 권장 보안 모범 사례는 스토리지 계정 키를 공유하지 않고, 가능하면 ID 기반 인증을 활용하는 것입니다.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Azure 파일 공유로 데이터를 가져올 때 디렉터리 및 파일 ACL 유지

Azure Files는 Azure 파일 공유에 데이터를 복사할 때 디렉터리 또는 파일 수준 ACL을 유지하도록 지원합니다. Azure 파일 동기화 또는 일반적인 파일 이동 도구 집합을 사용하여 디렉터리 또는 파일의 ACL을 Azure 파일 공유에 복사할 수 있습니다. 예를 들어 [robocopy](/windows-server/administration/windows-commands/robocopy)를 `/copy:s` 플래그와 함께 사용하여 데이터와 ACL을 Azure 파일 공유로 복사할 수 있습니다. ACL은 기본적으로 유지되므로 사용자가 ACL을 유지하기 위해 스토리지 계정에서 ID 기반 인증을 사용하도록 설정할 필요는 없습니다.

## <a name="pricing"></a>가격 책정
스토리지 계정에 대해 SMB를 통한 ID 기반 인증을 사용하도록 설정하는 데는 서비스 요금이 추가되지 않습니다. 가격에 대한 자세한 내용은 [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/) 및 [Azure AD Domain Services 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-ds/)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Files 및 SMB를 통한 ID 기반 인증에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [Azure 파일 공유에 대해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-enable.md)
- [Azure Files에서 Azure Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [FAQ](storage-files-faq.md)