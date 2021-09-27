---
title: Azure Virtual Desktop FSLogix 프로필 컨테이너 파일 - Azure
description: 이 문서에서는 Azure Virtual Desktop 및 Azure 파일 내의 FSLogix 프로필 컨테이너에 대해 설명합니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 93ef2ea1bcb10c08cfe6dc47027d12eeae3002b7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547488"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 프로필 컨테이너 및 Azure Files

Azure Virtual Desktop 서비스는 사용자 프로필 솔루션으로 FSLogix 프로필 컨테이너를 권장합니다. FSLogix는 Azure Virtual Desktop과 같은 원격 컴퓨팅 환경에서 프로필을 로밍하도록 설계되었습니다. 단일 컨테이너에 전체 사용자 프로필을 저장합니다. 로그인 시 이 컨테이너가 기본적으로 지원되는 VHD(가상 하드 디스크) 및 VHDX(Hyper-V 가상 하드 디스크)를 사용하여 컴퓨팅 환경에 동적으로 연결됩니다. 사용자 프로필은 즉시 제공되어 시스템에서 네이티브 사용자 프로필과 똑같이 표시됩니다. 이 문서에서는 Azure Virtual Desktop에서 Azure Files 함수를 사용하여 FSLogix 프로필 컨테이너를 사용하는 방법을 설명합니다.

> [!NOTE]
> Azure의 다양한 FSLogix 프로필 컨테이너 스토리지 옵션에 대한 비교 자료를 찾고 있다면 [FSLogix 프로필 컨테이너의 스토리지 옵션](store-fslogix-profile.md)을 참조하세요.

## <a name="user-profiles"></a>사용자 프로필

사용자 프로필에는 데스크톱 설정, 영구 네트워크 연결 및 애플리케이션 설정과 같은 구성 정보를 포함하여 개별에 대한 데이터 요소가 포함됩니다. 기본적으로 Windows는 운영 체제와 긴밀하게 통합되는 로컬 사용자 프로필을 만듭니다.

원격 사용자 프로필은 사용자 데이터와 운영 체제 간의 파티션을 제공합니다. 이를 통해 사용자 데이터에 영향을 주지 않고 운영 체제를 바꾸거나 변경할 수 있습니다. RDSH(원격 데스크톱 세션 호스트) 및 VDI(가상 데스크톱 인프라)에서 다음과 같은 이유로 운영 체제를 대체할 수 있습니다.

- 운영 체제 업그레이드
- 기존 VM(가상 머신) 대체
- 풀링된(비영구) RDSH 또는 VDI 환경에 속한 사용자

Microsoft 제품은 다음과 같은 기술을 포함하여 원격 사용자 프로필에 대한 몇 가지 기술로 운영됩니다.
- RUP(로밍 사용자 프로필)
- UPD(사용자 프로필 디스크)
- ESR(엔터프라이즈 상태 로밍)

UPD 및 RUP는 RDSH(원격 데스크톱 세션 호스트) 및 VHD(가상 하드 디스크) 환경에서 사용자 프로필에 대해 가장 널리 사용되는 기술입니다.

### <a name="challenges-with-previous-user-profile-technologies"></a>이전 사용자 프로필 기술과 관련된 문제

사용자 프로필에 대한 기존 및 레거시 Microsoft 솔루션에는 다양한 과제가 제공됩니다. 이전 솔루션이 RDSH 또는 VDI 환경과 함께 제공되는 모든 사용자 프로필 요구를 처리하지 않았습니다. 예를 들어 UPD는 많은 OST 파일을 처리할 수 없으며 RUP는 최신 설정을 유지하지 않습니다.

#### <a name="functionality"></a>기능

다음 표에서는 이전 사용자 프로필 기술의 이점과 제한 사항을 보여 줍니다.

| 기술 | 최신 설정 | Win32 설정 | OS 설정 | 사용자 데이터 | 서버 SKU에서 지원됨 | Azure의 백 엔드 스토리지 | 온-프레미스의 백 엔드 스토리지 | 버전 지원 | 후속 로그인 시간 |참고|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **UPD(사용자 프로필 디스크)** | 예 | 예 | 예 | 예 | 예 | 아니요 | 예 | Win 7+ | 예 | |
| **RUP(로밍 사용자 프로필), 유지 관리 모드** | 아니요 | 예 | 예 | 예 | 예| 아니요 | 예 | Win 7+ | No | |
| **ESR(Enterprise State Roaming)** | 예 | 아니요 | 예 | 아니요 | 참고 사항 보기 | 예 | 아니요 | Win 10 | No | 서버 SKU의 기능이 있지만 지원되는 사용자 인터페이스가 없음 |
| **UE-V(사용자 환경 가상화)** | 예 | 예 | 예 | 아니요 | 예 | 아니요 | 예 | Win 7+ | No |  |
| **OneDrive 클라우드 파일** | 아니요 | 아니요 | 아니요 | 예 | 참고 사항 보기 | 참고 사항 보기  | 참고 사항 보기 | Win 10 RS3 | No | 서버 SKU에서 테스트되지 않았습니다. Azure의 백 엔드 스토리지는 동기화 클라이언트에 따라 달라집니다. 백 엔드 스토리지 온-프레미스에 동기화 클라이언트가 필요합니다. |

#### <a name="performance"></a>성능

성능 요구 사항을 해결하려면 UPD에 [S2D(스토리지 공간 다이렉트)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)가 필요합니다. UPD는 SMB(서버 메시지 블록) 프로토콜을 사용합니다. 사용자가 기록되는 VM에 프로필을 복사합니다.

#### <a name="cost"></a>비용

S2D 클러스터는 필요한 성능을 구현하지만 기업 고객의 경우 비용이 많이 듭니다. 특히 SMB(중소기업) 고객의 경우 비용이 많이 듭니다. 이 솔루션의 경우 기업은 스토리지 디스크에 대해 요금을 지불하고 공유에 대한 디스크를 사용하는 VM의 비용을 지불합니다.

#### <a name="administrative-overhead"></a>관리 오버헤드

S2D 클러스터에는 보안 상태에서 패치, 업데이트 및 유지 관리되는 운영 체제가 필요합니다. 이러한 프로세스와 S2D 재해 복구 설정의 복잡성으로 인해 S2D는 전용 IT 직원이 있는 기업에만 적합할 수 있습니다.

## <a name="fslogix-profile-containers"></a>FSLogix 프로필 컨테이너

2018년 11월 19일에 [Microsoft에서 FSLogix를 얻었습니다](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix는 많은 프로필 컨테이너 문제를 해결합니다. 그 중 핵심은 다음과 같습니다.

- **성능:** [FSLogix 프로필 컨테이너](/fslogix/configure-profile-container-tutorial/)는 고성능이며 캐시된 교환 모드를 차단하는 성능 문제를 해결합니다.
- **OneDrive:** FSLogix 프로필 컨테이너가 없으면 비영구 RDSH 또는 VDI 환경에서 비즈니스용 OneDrive가 지원되지 않습니다. [OneDrive VDI 지원 페이지](/onedrive/sync-vdi-support)에서 상호 작용하는 방법을 알려줍니다. 자세한 내용은 [가상 데스크톱에서 동기화 클라이언트 사용](/deployoffice/rds-onedrive-business-vdi/)을 참조하세요.
- **추가 폴더:** FSLogix는 추가 폴더를 포함하도록 사용자 프로필을 확장하는 기능을 제공합니다.

취득 후 Microsoft는 UPD와 같은 기존 사용자 프로필 솔루션을 FSLogix 프로필 컨테이너와 바꾸기 시작했습니다.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Active Directory 도메인 서비스와 Azure Files 통합

FSLogix 프로필 컨테이너의 성능 및 기능은 클라우드를 활용합니다. 2019 7 월 7 일에 Microsoft Azure 파일은 [Azure Active Directory 도메인 서비스 (Azure AD DS)를 사용 하 여 Azure Files 인증](../storage/files/storage-files-active-directory-overview.md)의 일반 공급을 발표 했습니다. 비용 및 관리 오버헤드를 모두 해결함으로써 Azure AD DS 인증을 사용하는 Azure Files는 Azure Virtual Desktop 서비스의 사용자 프로필에 대한 프리미엄 솔루션입니다.

## <a name="best-practices-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대한 모범 사례

Azure Virtual Desktop은 고객이 사용 중인 VM의 크기, 유형 및 수에 대한 모든 권한을 제공합니다. 자세한 내용은 [이란?](overview.md)을 참조하세요.

Azure Virtual Desktop 환경이 모범 사례를 따르는지 확인하려면 다음을 수행합니다.

- Azure Files 스토리지 계정은 세션 호스트 VM과 동일한 지역에 있어야 합니다.
- Azure Files 권한은 [요구 사항 - 프로필 컨테이너](/fslogix/fslogix-storage-config-ht)에 설명된 권한과 일치해야 합니다.
- 각 호스트 풀 VM은 동일한 마스터 이미지에 기반하여 동일한 유형 및 크기 VM을 빌드해야 합니다.
- 관리, 크기 조정 및 업데이트를 지원하려면 각 호스트 풀 VM이 동일한 리소스 그룹에 있어야 합니다.
- 성능을 최적화하려면 스토리지 솔루션과 FSLogix 프로필 컨테이너가 동일한 데이터 센터 위치에 있어야 합니다.
- 마스터 이미지를 포함하는 스토리지 계정은 VM이 프로비저닝되는 동일한 지역 및 구독에 있어야 합니다.

## <a name="next-steps"></a>다음 단계

다음 가이드를 사용하여 Azure Virtual Desktop 환경을 설정합니다.

- 데스크톱 가상화 솔루션 빌드를 시작하려면 [ Virtual Desktop에서 테넌트 만들기](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)를 참조하세요.
- Azure Virtual Desktop 테넌트 내에서 호스트 풀을 만들려면 [Azure Marketplace를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)를 참조하세요.
- 클라우드에서 완전 관리형 파일 공유를 설정하려면 [Azure Files 공유 설정](/azure/storage/files/storage-files-active-directory-enable/)을 참조하세요.
- FSLogix 프로필 컨테이너를 구성하려면 [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)를 참조하세요.
- 호스트 풀에 사용자를 할당하려면 [ Virtual Desktop에 대한 앱 그룹 관리](manage-app-groups.md)를 참조하세요.
- 웹 브라우저에서 Azure Virtual Desktop 리소스에 액세스하려면 [ Virtual Desktop에 연결](./user-documentation/connect-web.md)을 참조하세요.
