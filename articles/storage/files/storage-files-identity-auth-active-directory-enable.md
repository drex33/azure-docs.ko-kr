---
title: 개요 - Azure 파일 공유에 대한 온-프레미스 AD DS 인증
description: Azure 파일 공유에 대한 AD DS(Active Directory Domain Services) 인증에 대해 알아봅니다. 이 문서에서는 지원 시나리오와 가용성을 살펴보고 AD DS와 Azure Active Directory 간의 사용 권한이 작동하는 방식을 설명합니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 03/15/2021
ms.author: rogarana
ms.openlocfilehash: 48813be024377ae3a75807feeaf101dca83be4c9
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481901"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>개요 - Azure 파일 공유에 대해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증

[Azure Files는](storage-files-introduction.md) 온-프레미스 AD DS(Active Directory Domain Services) 및 Azure AD DS(Azure Active Directory Domain Services)의 두 가지 유형의 도메인 서비스를 통해 SMB(서버 메시지 블록)를 통한 ID 기반 인증을 지원합니다. [작동 방법 섹션](./storage-files-active-directory-overview.md#how-it-works)을 검토하여 인증에 적합한 도메인 서비스를 선택하는 것이 좋습니다. 설정은 선택한 도메인 서비스에 따라 달라집니다. 이 문서는 Azure 파일 공유를 사용하여 인증을 위해 온-프레미스 AD DS를 사용하도록 설정하고 구성하는 방법에 중점을 둡니다.

Azure 파일 공유를 처음 사용하는 경우 다음 일련의 문서를 읽기 전에 [계획 가이드](storage-files-planning.md)를 읽어 보는 것이 좋습니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="supported-scenarios-and-restrictions"></a>지원되는 시나리오 및 제한 사항

- Azure Files 온-프레미스 AD DS 인증에 사용 되는 AD DS Id를 Azure AD에 동기화 하거나 기본 공유 수준 권한을 사용 해야 합니다. 암호 해시 동기화는 선택 사항입니다. 
- Azure 파일 동기화로 관리되는 Azure 파일 공유를 지원합니다.
- RC4-HMAC 및 [AES 256 암호화](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption)를 사용하는 AD로 Kerberos 인증을 지원합니다. AES 256 암호화 지원은 현재 이름이 15자 미만인 스토리지 계정으로 제한됩니다. AES 128 Kerberos 암호화는 아직 지원되지 않습니다.
- Single Sign-On 환경을 지원합니다.
- Windows 7 또는 Windows Server 2008 R2보다 최신 버전의 OS에서 실행되는 클라이언트 에서만 지원됩니다.
- 스토리지 계정이 등록된 AD 포리스트에 대해서만 지원됩니다. 기본적으로 단일 포리스트의 AD DS 자격 증명으로만 Azure 파일 공유에 액세스할 수 있습니다. 다른 포리스트에서 Azure 파일 공유에 액세스해야 하는 경우 적절한 포리스트 트러스트가 구성되어 있는지 확인하세요. 자세한 내용은 [FAQ](storage-files-faq.md#ad-ds--azure-ad-ds-authentication)를 참조하세요.
- AD DS에서 만든 컴퓨터 계정에 대한 인증을 지원하지 않습니다.
- NFS(네트워크 파일 시스템) 파일 공유에 대한 인증을 지원하지 않습니다.

SMB를 통해 Azure 파일 공유에 대한 AD DS를 사용하도록 설정하면 AD DS에 가입된 컴퓨터에서 기존 AD DS 자격 증명을 사용하여 Azure 파일 공유를 탑재할 수 있습니다. 온-프레미스 머신에서 호스팅되거나 Azure에서 호스팅되는 AD DS 환경에서 이 기능을 사용하도록 설정할 수 있습니다.

## <a name="videos"></a>동영상

몇 가지 일반적인 사용 사례에 대한 Azure Files AD 인증을 설정하는 데 도움이 되도록 다음 시나리오에 대한 단계별 지침을 포함하는 두 개의 비디오를 게시했습니다.

| 온-프레미스 파일 서버를 Azure Files로 바꾸기(파일 및 AD 인증을 위한 프라이빗 링크 설정 포함) | Windows Virtual Desktop에 대한 프로필 컨테이너로 Azure Files 사용(AD 인증 및 FsLogix 구성의 설정 포함)  |
|-|-|
| [![온-프레미스 파일 서버 교체 비디오의 동영상 가이드 - 재생하려면 클릭하세요.](./media/storage-files-identity-auth-active-directory-enable/replace-on-prem-server-thumbnail.png)](https://www.youtube.com/watch?v=jd49W33DxkQ) | [![프로필 컨테이너로 Azure Files 사용 비디오의 동영상 가이드 - 재생하려면 클릭하세요.](./media/storage-files-identity-auth-active-directory-enable/files-ad-ds-fslogix-thumbnail.png)](https://www.youtube.com/watch?v=9S5A1IJqfOQ) |


## <a name="prerequisites"></a>필수 구성 요소 

Azure 파일 공유에 대해 AD DS 인증을 사용하도록 설정하기 전에 다음 사전 요구 사항을 완료했는지 확인합니다. 

- [AD DS 환경](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)을 선택하거나 만들고 Azure AD Connect를 사용하여 [Azure AD에 동기화](../../active-directory/hybrid/how-to-connect-install-roadmap.md)합니다. 

    신규 또는 기존 온-프레미스 AD DS 환경에서 이 기능을 사용하도록 설정할 수 있습니다. 액세스에 사용 되는 id를 Azure AD에 동기화 하거나 기본 공유 수준 권한을 사용 해야 합니다. Azure AD 테넌트와 액세스 중인 파일 공유는 동일한 구독에 연결되어 있어야 합니다.

- 온-프레미스 컴퓨터 또는 Azure VM을 온-프레미스 AD DS에 도메인 가입합니다. 도메인에 가입하는 방법에 대한 자세한 내용은 [컴퓨터를 도메인에 가입](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)을 참조하세요.

    컴퓨터가 AD DS 도메인에 가입되어 있지 않은 경우에도 AD 도메인 컨트롤러가 컴퓨터의 가시선에 있는 경우 인증을 위해 AD 자격 증명을 계속 활용할 수 있습니다.

- Azure 스토리지 계정을 선택하거나 만듭니다.  성능을 최적화하려면 공유에 액세스하려는 클라이언트와 동일한 지역에 스토리지 계정을 배치하는 것이 좋습니다. 그런 다음 스토리지 계정 키를 사용하여 [Azure 파일 공유를 탑재](storage-how-to-use-files-windows.md)합니다. 스토리지 계정 키를 사용하여 탑재하면 연결이 확인됩니다.

    파일 공유가 포함된 스토리지 계정이 Azure AD DS 인증에 대해 아직 구성되지 않았는지 확인합니다. Azure Files Azure AD DS 인증이 스토리지 계정에서 활성화된 경우 온-프레미스 AD DS를 사용하도록 변경하기 전에 비활성화해야 합니다. 이는 적절한 권한 적용을 위해 Azure AD DS 환경에서 구성된 기존 ACL을 다시 구성해야 함을 의미합니다.


    Azure Files에 연결하는 데 문제가 발생하는 경우 [Windows에서 발생하는 Azure Files 탑재 오류를 위해 게시된 문제 해결 도구](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)를 참조하세요. 또한 포트 445가 차단되는 시나리오를 해결하기 위한 [지침](./storage-files-faq.md#on-premises-access)을 제공합니다. 


- Azure 파일 공유에 대한 AD DS 인증을 설정하 고 구성하기 전에 관련 네트워킹 구성을 수행합니다. 자세한 내용은 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조하세요.

## <a name="regional-availability"></a>국가별 가용성

AD DS를 사용한 Azure Files 인증은 [모든 Azure 공용, 중국 및 Gov 지역](https://azure.microsoft.com/global-infrastructure/locations/)에서 사용할 수 있습니다.

## <a name="overview"></a>개요

파일 공유에서 네트워킹 구성을 사용하도록 설정하려는 경우 [네트워킹 고려 사항](./storage-files-networking-overview.md) 문서를 읽고 AD DS 인증을 사용하도록 설정하기 전에 관련 구성을 완료하는 것이 좋습니다.

Azure 파일 공유에 대해 AD DS 인증을 사용하도록 설정하면 온-프레미스 AD DS 자격 증명을 사용하여 Azure 파일 공유에 인증할 수 있습니다. 또한 세분화된 액세스 제어를 허용하도록 권한을 더 잘 관리할 수 있습니다. 이렇게 하려면 AD 연결을 사용하여 온-프레미스 AD DS에서 Azure AD로 ID를 동기화해야 합니다. 온-프레미스 AD DS 자격 증명을 사용하여 파일/공유 수준 액세스를 관리하는 동안 Azure AD에 동기화된 ID로 공유 수준 액세스를 제어합니다.

그런 다음 아래 단계에 따라 AD DS 인증을 위한 Azure Files를 설정합니다. 

1. [1부: 스토리지 계정에서 AD DS 인증 사용](storage-files-identity-ad-ds-enable.md)

1. [2부: 대상 AD ID와 동기화되는 Azure AD ID(사용자, 그룹 또는 서비스 주체)에 공유에 대한 액세스 권한 할당](storage-files-identity-ad-ds-assign-permissions.md)

1. [3부: 디렉터리 및 파일에 대해 SMB를 통한 Windows ACL 구성](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [4부: AD DS에 연결된 VM에 Azure 파일 공유 탑재](storage-files-identity-ad-ds-mount-file-share.md)

1. [AD DS에서 스토리지 계정 ID의 암호를 업데이트](storage-files-identity-ad-ds-update-password.md)

다음 다이어그램에서는 Azure 파일 공유에 대해 SMB를 통한 Azure AD 인증을 사용하도록 설정하는 엔드투엔드 워크플로를 보여 줍니다. 

![Files AD 워크플로 다이어그램](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Azure 파일 공유에 액세스하는 데 사용되는 ID를 Azure AD에 동기화하여 [Azure 역할 기반 액세스 제어(Azure RBAC)](../../role-based-access-control/overview.md) 모델을 통해 공유 수준 파일 권한을 적용해야 합니다. 또는 기본 공유 수준 권한을 사용할 수 있습니다. 기존 파일 서버에서 전달된 파일/디렉터리의 [Windows 스타일 DACL](/previous-versions/technet-magazine/cc161041(v=msdn.10))은 보존되고 적용됩니다. 이를 통해 엔터프라이즈 AD DS 환경과 원활한 통합이 가능합니다. 온-프레미스 파일 서버를 Azure 파일 공유로 교체하면 기존 사용자가 사용 중인 자격 증명을 변경하지 않고도 Single Sign-On 환경을 사용하여 현재 클라이언트에서 Azure 파일 공유에 액세스할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

Azure 파일 공유에 대해 온-프레미스 AD DS 인증을 사용하도록 설정하려면 다음 문서를 계속 진행합니다.

[1부: 계정에 대해 Azure AD DS 인증 사용](storage-files-identity-ad-ds-enable.md)
