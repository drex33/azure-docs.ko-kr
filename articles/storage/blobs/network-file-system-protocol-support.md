---
title: Azure Blob Storage의 네트워크 파일 시스템 3.0 지원 | Microsoft Docs
description: 이제 Blob Storage는 NFS(네트워크 파일 시스템) 3.0 프로토콜을 지원합니다. 이 지원을 통해 Linux 클라이언트는 Azure VM(가상 머신) 또는 온-프레미스에서 실행되는 컴퓨터에서 Blob Storage에 컨테이너를 탑재할 수 있습니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: f350b4fad79a80a1bba0572c5ed906aa4aff1168
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005459"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원

이제 Blob Storage는 NFS(네트워크 파일 시스템) 3.0 프로토콜을 지원합니다. 이 지원 기능은 개체 스토리지 규모 및 가격으로 Linux 파일 시스템 호환성을 제공하고, Linux 클라이언트가 Azure VM(가상 머신) 또는 온-프레미스 컴퓨터에서 Blob 저장소에 컨테이너를 탑재할 수 있게 해줍니다. 

클라우드에서 HPC(고성능 컴퓨팅)와 같은 대규모 레거시 워크로드를 실행하는 것은 항상 어려운 일이었습니다. 한 가지 이유는 애플리케이션에서 NFS 또는 SMB(서버 메시지 블록)와 같은 기존 파일 프로토콜을 사용하여 데이터에 액세스하는 경우가 많기 때문입니다. 또한 계층 구조 네임스페이스와 효율적인 메타 데이터 작업을 제공하는 파일 시스템 대신 단일 구조 네임스페이스와 광범위한 메타데이터가 있는 개체 스토리지에 초점을 맞춘 네이티브 클라우드 스토리지 서비스입니다. 

Blob Storage는 이제 계층 구조 네임스페이스를 지원하며, NFS 3.0 프로토콜 지원과 함께 사용할 경우 Azure에서 대규모 클라우드 개체 스토리지를 기반으로 레거시 애플리케이션을 훨씬 더 쉽게 실행할 수 있게 해줍니다. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>이 기능에 적합한 애플리케이션 및 워크로드

NFS 3.0 프로토콜 기능은 미디어 처리, 위험 시뮬레이션, 유전체학 시퀀싱과 같이 처리량이 많고, 규모가 크며, 읽기 집약적 워크로드를 수행하는 데 가장 적합합니다. 여러 판독기와 많은 스레드를 사용하며 높은 대역폭이 필요한 다른 워크로드 유형에는 이 기능을 사용하는 것이 좋습니다. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3.0 및 계층 구조 네임스페이스

NFS 3.0 프로토콜 지원을 위해서는 Blob을 계층 구조 네임스페이스로 구성해야 합니다. 스토리지 계정을 만들 때 계층 구조 네임스페이스를 사용하도록 설정할 수 있습니다. 계층 구조 네임스페이스를 사용하는 기능은 Azure Data Lake Storage Gen2에서 도입되었습니다. 이렇게 하면 개체(파일)가 컴퓨터의 파일 시스템이 구성되는 것과 동일한 방식으로 디렉터리 및 하위 디렉터리의 계층 구조로 구성됩니다.  계층 구조 네임스페이스는 선형으로 확장되며 데이터 용량 또는 성능을 저하시키지 않습니다. 계층 구조 네임스페이스에서 서로 다른 프로토콜이 확장됩니다. NFS 3.0 프로토콜은 이러한 사용 가능한 프로토콜 중 하나입니다.   

> [!div class="mx-imgBorder"]
> ![계층 구조 네임스페이스](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>블록 Blob으로 저장되는 데이터

NFS 3.0 프로토콜 지원을 사용하도록 설정하면 스토리지 계정의 모든 데이터가 블록 Blob으로 저장됩니다. 블록 Blob은 대량의 읽기 집약적 데이터를 효율적으로 처리하는 데 최적화되어 있습니다. 블록 Blob은 블록으로 구성되어 있습니다. 각 블록은 블록 ID로 식별됩니다. 블록 Blob은 최대 50,000개 블록을 포함할 수 있습니다. 블록 Blob의 각 블록은 계정에서 사용하는 서비스 버전에 허용되는 최대 크기까지 크기를 변경할 수 있습니다.

애플리케이션에서 NFS 3.0 프로토콜을 사용하여 요청을 수행하는 경우 해당 요청은 블록 Blob 작업의 조합으로 변환됩니다. 예를 들어 NFS 3.0 읽기 RPC(원격 프로시저 호출) 요청은 [Get Blob](/rest/api/storageservices/get-blob) 작업으로 변환됩니다. NFS 3.0 쓰기 RPC 요청은 [Get Block List](/rest/api/storageservices/get-block-list), [Put Block](/rest/api/storageservices/put-block) 및 [Put Block List](/rest/api/storageservices/put-block-list)의 조합으로 변환됩니다.

## <a name="general-workflow-mounting-a-storage-account-container"></a>일반 워크플로: 스토리지 계정 컨테이너 탑재

Linux 클라이언트는 Azure VM(가상 머신) 또는 온-프레미스 컴퓨터에서 Blob Storage에 컨테이너를 탑재할 수 있습니다. 스토리지 계정 컨테이너를 탑재하려면 다음 작업을 수행해야 합니다.

1. Azure VNet(가상 네트워크)을 만듭니다.

2. 네트워크 보안을 구성합니다.

3. VNet의 트래픽만 허용하는 스토리지 계정을 만들고 구성합니다.

4. 스토리지 계정에 컨테이너를 만듭니다.

5. 컨테이너를 탑재합니다.

단계별 지침은 [NFS(네트워크 파일 시스템) 3.0 프로토콜을 사용하여 Blob Storage 탑재](network-file-system-protocol-support-how-to.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

트래픽은 VNet에서 시작되어야 합니다. 클라이언트는 VNet을 통해 스토리지 계정에 안전하게 연결할 수 있습니다. 계정에서 데이터를 보호하는 유일한 방법은 VNet 및 기타 네트워크 보안 설정을 사용하는 것입니다. 계정 키 인증, Azure AD(Azure Active Directory) 보안 및 ACL(액세스 제어 목록)을 포함하여 데이터를 보호하는 데 사용되는 다른 모든 도구는 NFS 3.0 프로토콜 지원이 설정된 계정에서 아직 지원되지 않습니다. 

자세히 알아보려면 [Blob Storage에 대한 네트워크 보안 권장 사항](security-recommendations.md#networking)을 참조하세요.

### <a name="supported-network-connections"></a>지원되는 네트워크 연결

클라이언트는 퍼블릭 또는 [프라이빗 엔드포인트](../common/storage-private-endpoints.md)을 통해 연결할 수 있으며 다음 네트워크 위치 중 하나에서 연결할 수 있습니다.

- 스토리지 계정에 대해 구성한 VNet 

  이 문서에서는 이 VNet을 *기본 VNet* 이라고 합니다. 자세한 내용은 [가상 네트워크에서 액세스 권한 얻기](../common/storage-network-security.md#grant-access-from-a-virtual-network)를 참조하세요.

- 기본 VNet과 동일한 지역에 있는 피어링된 VNet

  이 피어링된 VNet에 대한 액세스를 허용하도록 스토리지 계정을 구성해야 합니다. 자세한 내용은 [가상 네트워크에서 액세스 권한 얻기](../common/storage-network-security.md#grant-access-from-a-virtual-network)를 참조하세요.

- [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute 게이트웨이](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)를 사용하여 기본 VNet에 연결된 온-프레미스 네트워크 

  자세한 내용은 [온-프레미스 네트워크에서의 액세스 구성](../common/storage-network-security.md#configuring-access-from-on-premises-networks)을 참조하세요.

- 피어링된 네트워크에 연결된 온-프레미스 네트워크

  [게이트웨이 전송](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)과 함께 [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute 게이트웨이](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)를 사용하여 이 작업을 수행할 수 있습니다. 

> [!IMPORTANT]
> 온-프레미스 네트워크에서 연결하는 경우 클라이언트가 111 및 2048 포트를 통해 나가는 통신을 허용하는지 확인합니다. NFS 3.0 프로토콜은 이러한 포트를 사용합니다.

<a id="azure-storage-features-not-yet-supported"></a>

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

현재 릴리스의 NFS 3.0 지원과 관련된 문자와 제한 사항의 전체 목록은 [알려진 문제](network-file-system-protocol-known-issues.md) 문서를 참조하세요.

## <a name="pricing"></a>가격 책정

데이터 스토리지 및 트랜잭션 비용에 관해서는 [Azure Blob Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 페이지를 참조하세요. 

## <a name="see-also"></a>참고 항목

- [NFS(네트워크 파일 시스템) 3.0 프로토콜을 사용하여 Blob Storage 탑재](network-file-system-protocol-support-how-to.md)
- [Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 성능 고려 사항](network-file-system-protocol-support-performance.md)
- [NFS를 사용한 Azure Files, Blob Storage, Azure NetApp Files에 대한 액세스 비교](../common/nfs-comparison.md)
