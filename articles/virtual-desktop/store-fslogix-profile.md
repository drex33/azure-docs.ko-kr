---
title: 스토리지 FSLogix 프로필 컨테이너 Azure Virtual Desktop - Azure
description: Azure Storage에 Azure Virtual Desktop FSLogix 프로필을 저장하는 옵션입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ba1ae8c108f02dbd104361e1efe39f5583011124
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111755702"
---
# <a name="storage-options-for-fslogix-profile-containers-in-azure-virtual-desktop"></a>Azure Virtual Desktop의 FSLogix 프로필 컨테이너에 대한 스토리지 옵션

Azure는 FSLogix 프로필 컨테이너를 저장하는 데 사용할 수 있는 여러 스토리지 솔루션을 제공합니다. 이 문서에서는 Azure에서 Azure Virtual Desktop FSLogix 사용자 프로필 컨테이너에 대해 제공하는 스토리지 솔루션을 비교합니다. 대부분의 고객에게 Azure Files에 FSLogix 프로필 컨테이너를 저장하도록 권장합니다.

Azure Virtual Desktop은 FSLogix 프로필 컨테이너를 권장 사용자 프로필 솔루션으로 제공합니다. FSLogix는 Azure Virtual Desktop과 같은 원격 컴퓨팅 환경에서 프로필을 로밍하도록 설계되었습니다. 로그인 시 이 컨테이너가 기본적으로 지원되는 VHD(가상 하드 디스크) 및 VHDX(Hyper-V 가상 하드 디스크)를 사용하여 컴퓨팅 환경에 동적으로 연결됩니다. 사용자 프로필은 즉시 제공되어 시스템에서 네이티브 사용자 프로필과 똑같이 표시됩니다.

다음 표에서는 Azure Storage가 Azure Virtual Desktop FSLogix 프로필 컨테이너 사용자 프로필에 대해 제공하는 스토리지 솔루션을 비교합니다.

## <a name="azure-platform-details"></a>Azure 플랫폼 세부 정보

|기능|Azure 파일|Azure NetApp Files|직접 스토리지 공간|
|--------|-----------|------------------|---------------------|
|사용 사례|범용 가상 컴퓨터|NetApp 온-프레미스에서 마이그레이션 또는 Ultra 성능|플랫폼 간|
|플랫폼 서비스|예, Azure 네이티브 솔루션|예, Azure 네이티브 솔루션|아니요, 자체 관리|
|국가별 가용성|모든 지역|[지역 선택](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|모든 지역|
|중복|로컬 중복/영역 중복/지역 중복/지역 영역 중복|로컬 중복|로컬 중복/영역 중복/지역 중복|
|계층 및 성능| 표준(트랜잭션 최적화)<br>Premium<br>공유당 최대 100K IOPS, 공유당 10GBps, 약 3ms의 대기 시간|Standard<br>Premium<br>Ultra<br>볼륨당 최대 4.5GBps, 약 1ms의 대기 시간. IOPS 및 성능 세부 정보는 [Azure NetApp Files 성능 고려 사항](../azure-netapp-files/azure-netapp-files-performance-considerations.md) 및 [FAQ](../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops)를 참조하세요.|표준 HDD: 디스크당 최대 500 IOPS 제한<br>표준 SSD: 디스크당 최대 4k IOPS 제한<br>프리미엄 SSD: 디스크당 최대 20k IOPS 제한<br>스토리지 공간 다이렉트 프리미엄 디스크를 권장합니다.|
|용량|공유당 100TiB, 범용 계정당 최대 5PiB |볼륨당 100TiB, 구독당 최대 12.5PiB|디스크당 최대 32TiB|
|필요한 인프라|최소 공유 크기 1GiB|최소 용량 풀 4TiB, 최소 볼륨 크기 100GiB|Azure IaaS(+ 클라우드 감시)의 VM 2개 또는 디스크 비용 없이 VM 3개 이상|
|프로토콜|SMB 3.0/2.1, NFSv4.1(미리 보기), REST|NFSv3, NFSv4.1(미리 보기), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Azure 관리 세부 정보

|기능|Azure 파일|Azure NetApp Files|직접 스토리지 공간|
|--------|-----------|------------------|---------------------|
|Access|클라우드, 온-프레미스 및 하이브리드(Azure 파일 동기화)|클라우드, 온-프레미스(ExpressRoute를 통해)|클라우드, 온-프레미스|
|Backup|Azure 백업 스냅샷 통합|Azure NetApp Files 스냅샷|Azure 백업 스냅샷 통합|
|보안 및 규정 준수|[모든 Azure 지원 인증서](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO 완료|[모든 Azure 지원 인증서](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory 통합|[네이티브 Active Directory 및 Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services 및 네이티브 Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|네이티브 Active Directory 또는 Azure Active Directory Domain Services 지원만|

스토리지 메서드를 선택했으면 [Azure Virtual Desktop 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)에서 가격 책정 계획에 대한 정보를 확인하세요.

## <a name="azure-files-tiers"></a>Azure Files 계층

Azure Files는 프리미엄 및 표준이라는 두 가지 스토리지 계층을 제공합니다. 이러한 계층을 통해 시나리오 요구 사항에 맞게 파일 공유의 성능과 비용을 조정할 수 있습니다.

- 프리미엄 파일 공유는 SSD(반도체 드라이브)로 지원되며 FileStorage 스토리지 계정 유형에서 배포됩니다. 프리미엄 파일 공유는 IO(입력 및 출력) 집약적 워크로드에 대해 일관된 고성능 및 짧은 대기 시간을 제공합니다. 

- 표준 파일 공유는 HDD(하드 디스크 드라이브)로 지원되며 GPv2(범용 버전 2) 스토리지 계정 유형으로 배포됩니다. 표준 파일 공유는 범용 파일 공유, 개발/테스트 환경 등 성능 변동성에 영향을 그다지 받지 않는 IO 워크로드에 대해 안정적인 성능을 제공합니다. 종량제 청구 모델에서만 제공됩니다.

다음 표에서는 워크로드에 따라 사용할 성능 계층에 대한 권장 사항을 보여줍니다. 이러한 권장 사항은 성능 목표, 예산 및 지역별 고려 사항을 충족하는 성능 계층을 선택하는 데 도움이 됩니다. 이러한 권장 사항은 [원격 데스크톱 워크로드 유형](/windows-server/remote/remote-desktop-services/remote-desktop-workloads)의 예제 시나리오를 기반으로 합니다. 

| 워크로드 유형 | 권장 파일 계층 |
|--------|-----------|
| 라이트(사용자 200명 미만) | 표준 파일 공유 |
| 라이트(사용자 200명 초과) | 여러 파일 공유를 사용하는 프리미엄 파일 공유 또는 표준 |
|중간|프리미엄 파일 공유|
|Heavy|프리미엄 파일 공유|
|전력|프리미엄 파일 공유|

Azure Files 성능에 대한 자세한 내용은 [파일 공유 및 파일 크기 조정 목표](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets)를 참조하세요. 가격 책정에 대한 자세한 내용은 [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

FSLogix 프로필 컨테이너, 사용자 프로필 디스크 및 기타 사용자 프로필 기술에 대해 자세히 알아보려면 [FSLogix 프로필 컨테이너 및 Azure 파일](fslogix-containers-azure-files.md)의 표를 참조하세요.

자체 FSLogix 프로필 컨테이너를 만들 준비가 되면 다음 자습서 중 하나를 시작하세요.

- [Azure Virtual Desktop의 Azure Files에서 FSLogix 프로필 컨테이너 시작](create-file-share.md)
- [Azure NetApp 파일을 사용하여 호스트 풀에 대한 FSLogix 프로필 컨테이너 만들기](create-fslogix-profile-container.md)
- [Azure에서 UPD 스토리지에 대해 2노드 스토리지 공간 다이렉트 스케일 아웃 파일 서버 배포](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)의 지침은 사용자 프로필 디스크 대신 FSLogix 프로필 컨테이너를 사용할 때도 적용됩니다.

[Azure Virtual Desktop에서 테넌트 만들기](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)에서 처음부터 시작하여 자체 Azure Virtual Desktop 솔루션을 설정할 수도 있습니다.
