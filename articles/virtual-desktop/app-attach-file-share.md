---
title: Azure Virtual Desktop 설정 파일 공유 MSIX 앱 연결 - Azure
description: Azure Virtual Desktop에 맞게 MSIX 앱 연결에 대한 파일 공유를 설정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: e0ba3363b6004afb8e42f420fae681b2827106fb
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707088"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>MSIX 앱 연결에 대한 파일 공유 설정

모든 MSIX 이미지는 읽기 전용 권한이 있는 호스트 풀의 사용자가 액세스할 수 있는 네트워크 공유에 저장되어야 합니다.

MSIX 앱 연결에는 파일 공유에서 사용하는 스토리지 패브릭 유형에 대한 종속성이 없습니다. MSIX 앱 연결 공유에 대한 고려 사항은 FSLogix 공유와 동일합니다. 스토리지 요구 사항에 대한 자세한 내용은 [Azure Virtual Desktop의 FSLogix 프로필 컨테이너에 대한 스토리지 옵션](store-fslogix-profile.md)을 참조하세요.

## <a name="performance-requirements"></a>성능 요구 사항

시스템에 대한 MSIX 앱 연결 이미지 크기 제한은 VHD 또는 VHDx 파일을 저장하는 데 사용하는 스토리지 유형과 VHD, VHDX 또는 CIM 파일 및 파일 시스템의 크기 제한에 따라 달라집니다.

다음 표에서는 각 VM에 대해 하나의 MSIX 앱이 있는 단일 1GB MSIX 이미지에 필요한 리소스 수에 대한 예제를 제공합니다.

| 리소스             | 요구 사항 |
|----------------------|--------------|
| 안정적인 상태 IOPs    | 1 IOPs       |
| 머신 부팅 로그인 | 10 IOPs      |
| 대기 시간              | 400ms       |

요구 사항은 MSIX 이미지에 저장되는 MSIX 패키지 애플리케이션의 수에 따라 크게 달라질 수 있습니다. 더 큰 MSIX 이미지의 경우 더 많은 대역폭을 할당해야 합니다.

### <a name="storage-recommendations"></a>스토리지 권장 사항

Azure는 MISX 앱 연결에 사용할 수 있는 여러 스토리지 옵션을 제공합니다. Azure Files 또는 Azure NetApp Files를 사용하는 것이 좋습니다. 이러한 옵션은 비용과 관리 오버헤드 사이에서 최상의 가치를 제공합니다. [Azure Virtual Desktop의 FSLogix 프로필 컨테이너에 대한 스토리지 옵션](store-fslogix-profile.md) 문서는 Azure에서 제공하는 다른 관리되는 스토리지 솔루션을 Azure Virtual Desktop의 컨텍스트에서 비교합니다.

### <a name="optimize-msix-app-attach-performance"></a>MSIX 앱 연결 성능 최적화

MSIX 앱 연결 성능을 최적화하기 위해 수행하는 몇 가지 다른 작업은 다음과 같습니다.

- MSIX 앱 연결에 사용하는 스토리지 솔루션은 세션에서 호스팅하는 것과 동일한 데이터 센터 위치에 있어야 합니다.
- 성능 병목 현상을 방지하려면 다음 VHD, VHDX 및 CIM 파일을 바이러스 백신 검사에서 제외합니다.
   
    - <MSIXAppAttachFileShare\>\*.VHD
    - <MSIXAppAttachFileShare\>\*.VHDX
    - \\\\storageaccount.file.core.windows.net\\share\*\*.VHD
    - \\\\storageaccount.file.core.windows.net\\share\*\*.VHDX
    - <MSIXAppAttachFileShare>.CIM
    - \\\\storageaccount.file.core.windows.net\\share\*\*.CIM

- FSLogix 프로필 컨테이너에서 MSIX 앱 연결에 대한 스토리지 패브릭을 분리합니다.
- 모든 VM 시스템 계정 및 사용자 계정에는 파일 공유에 액세스할 수 있는 읽기 전용 권한이 있어야 합니다.
- Azure Virtual Desktop에 대한 모든 재해 복구 계획은 보조 장애 조치(failover) 위치에 MSIX 앱 연결 파일 공유 복제를 포함해야 합니다. 재해 복구에 대해 자세히 알아보려면 [비즈니스 연속성 및 재해 복구 계획 설정](disaster-recovery.md)을 참조하세요.

## <a name="how-to-set-up-the-file-share"></a>파일 공유를 설정하는 방법

MSIX 앱 연결 파일 공유에 대한 설치 프로세스는 대부분 [FSLogix 프로필 파일 공유에 대한 설치 프로세스](create-host-pools-user-profile.md)와 동일합니다. 그러나 사용자에게 다른 권한을 할당해야 합니다. MSIX 앱 연결에는 파일 공유에 액세스하기 위한 읽기 전용 권한이 필요합니다.

Azure Files에서 MSIX 애플리케이션을 저장하는 경우 세션 호스트의 경우 모든 세션 호스트 VM을 할당해야 합니다. 스토리지 계정 RBAC(역할 기반 액세스 제어)와 공유에 대한 파일 공유 NTFS(새 기술 파일 시스템) 권한을 모두 할당해야 합니다.

| Azure 개체                      | 필수 역할                                     | 역할 함수                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| 세션 호스트(VM 컴퓨터 개체)| 스토리지 파일 데이터 SMB 공유 Contributor          | 읽기 및 실행, 읽기, 폴더 콘텐츠 나열  |
| 파일 공유의 관리자              | 스토리지 파일 데이터 SMB 공유 관리자 권한 Contributor | 모든 권한                                  |
| 파일 공유의 사용자               | 스토리지 파일 데이터 SMB 공유 Contributor          | 읽기 및 실행, 읽기, 폴더 콘텐츠 나열  |

스토리지 계정 및 파일 공유에 대한 세션 호스트 VM 권한을 할당하려면:

1. AD DS(Active Directory Domain Services) 보안 그룹을 만듭니다.

2. 모든 세션 호스트 VM에 대한 컴퓨터 계정을 그룹의 멤버로 추가합니다.

3. AD DS 그룹을 Azure AD(Azure Active Directory)에 동기화합니다.

4. 스토리지 계정을 만듭니다.

5. [Azure 파일 공유 만들기](../storage/files/storage-how-to-create-file-share.md#create-a-file-share)의 지침에 따라 스토리지 계정에서 파일 공유를 만듭니다.

6. [1부: Azure 파일 공유에 대한 AD DS 인증 사용](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)의 지침에 따라 스토리지 계정을 AD DS에 조인합니다.

7. Azure AD에 동기화된 AD DS 그룹을 할당하고, 스토리지 계정에 스토리지 파일 데이터 SMB 공유 기여자 역할을 할당합니다.

8. [2부: ID에 공유 수준 권한 할당](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)의 지침에 따라 세션 호스트에 파일 공유를 탑재합니다.

9. 파일 공유에 대한 NTFS 권한을 AD DS 그룹에 부여합니다.

10. 사용자 계정에 대한 NTFS 사용 권한을 설정합니다. VM의 계정이 속한 AD DS에서 제공하는 OU(운영 단위)가 필요합니다.

스토리지에 ID를 할당한 후 [다음 단계](#next-steps) 문서에 설명된 지침에 따라 VM에 할당한 ID에 다른 필요한 권한을 부여합니다.

또한 세션 호스트 VM에 NTFS(새 기술 파일 시스템) 권한이 있는지 확인해야 합니다. AD DS(Active Directory Domain Services)에서 제공하는 작업 단위 컨테이너가 있어야 하고 사용자가 이러한 권한을 사용하려면 해당 운영 단위의 구성원이어야 합니다.

## <a name="next-steps"></a>다음 단계

파일 공유를 설정한 후에 수행해야 하는 다른 작업은 다음과 같습니다.

- [Azure Files 및 AD DS를 사용하여 프로필 컨테이너 만들기](create-file-share.md)에서 Azure AD DS(Active Directory Domain Services)를 설정하는 방법에 대해 알아봅니다.
- [Azure Files 및 Azure AD DS를 사용하여 프로필 컨테이너 만들기](create-profile-container-adds.md)에서 Azure Files 및 Azure AD DS를 설정하는 방법에 대해 알아봅니다.
- [Azure NetApp Files 및 AD DS를 사용하여 프로필 컨테이너 만들기](create-fslogix-profile-container.md)에서 MSIX 앱 연결에 대해 Azure NetApp Files를 설정하는 방법에 대해 알아봅니다.
- [파일 공유를 사용하여 호스트 풀의 프로필 컨테이너 만들기](create-host-pools-user-profile.md)에서 가상 머신 기반 파일 공유를 사용하는 방법에 대해 알아봅니다.

완료되면 다음과 같은 도움이 될 수 있는 몇 가지 다른 리소스를 확인할 수 있습니다.

- [Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 이 기능에 대해 커뮤니티에 질문합니다.
- [Azure Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Azure Virtual Desktop에 대한 피드백을 남길 수도 있습니다.
- [MSIX 앱 연결 용어](app-attach-glossary.md)
- [MSIX 앱 연결 FAQ](app-attach-faq.yml)
