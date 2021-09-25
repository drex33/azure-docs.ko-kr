---
title: 프라이빗 엔드포인트 사용
titleSuffix: Azure Storage
description: 가상 네트워크에서 스토리지 계정에 안전하게 액세스하기 위한 프라이빗 엔드포인트의 개요입니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: a52460db452d519c51fb7a1b191766b21da67f88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592283"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Azure Storage에 프라이빗 엔드포인트 사용

Azure Storage 계정에 [프라이빗 엔드포인트](../../private-link/private-endpoint-overview.md)를 사용하여 VNet(가상 네트워크)의 클라이언트가 [프라이빗 링크](../../private-link/private-link-overview.md)를 통해 안전하게 데이터에 액세스하도록 할 수 있습니다. 프라이빗 엔드포인트는 스토리지 계정 서비스용 VNet 주소 공간의 IP 주소를 사용합니다. VNet에 있는 클라이언트와 스토리지 계정 사이의 네트워크 트래픽은 VNet과 Microsoft 백본 네트워크의 프라이빗 링크를 가로지르며 퍼블릭 인터넷 노출을 방지합니다.

스토리지 계정에 대해 프라이빗 엔드포인트를 사용하면 다음 작업을 수행할 수 있습니다.

- 스토리지 서비스용 퍼블릭 엔드포인트의 모든 연결을 차단하도록 스토리지 방화벽을 구성해 스토리지 계정을 보호합니다.
- VNet(가상 네트워크)에서의 데이터 반출을 차단하도록 하여 VNet 보안을 강화합니다.
- 개인 피어링을 사용하여 [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoutes](../../expressroute/expressroute-locations.md)를 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 스토리지 계정에 안전하게 연결합니다.

## <a name="conceptual-overview"></a>개념적 개요

![Azure Storage에 대한 프라이빗 엔드포인트의 개요](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

프라이빗 엔드포인트는 VNet([Virtual Network](../../virtual-network/virtual-networks-overview.md))의 Azure 서비스에 대한 특별 네트워크 인터페이스입니다. 스토리지 계정에 프라이빗 엔드포인트를 만들 때 VNet 및 스토리지의 클라이언트 간에 보안 연결을 제공합니다. 프라이빗 엔드포인트에는 VNet의 IP 주소 범위 내에서 IP 주소가 할당됩니다. 프라이빗 엔드포인트와 스토리지 서비스 간의 연결은 보안 프라이빗 링크를 사용합니다.

VNet의 애플리케이션은 **다른 방법으로 사용하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용하여** 프라이빗 엔드포인트를 통해 스토리지 서비스에 원활하게 연결할 수 있습니다. 프라이빗 엔드포인트는 REST와 SMB를 포함하여 스토리지 계정에서 지원하는 모든 프로토콜과 함께 사용할 수 있습니다.

[서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하는 서브넷에서 프라이빗 엔드포인트를 만들 수 있습니다. 따라서 서브넷의 클라이언트는 서비스 엔드포인트를 사용해 다른 스토리지 계정에 액세스하는 동안 프라이빗 엔드포인트를 사용해 하나의 스토리지 계정에 연결할 수 있습니다.

VNet에서 스토리지 서비스에 대한 프라이빗 엔드포인트를 만들면 스토리지 계정 소유자에게 승인을 위해 동의 요청이 전송됩니다. 프라이빗 엔드포인트 만들기를 요청한 사용자가 스토리지 계정의 소유자인 경우 이 동의 요청이 자동으로 승인됩니다.

스토리지 계정 소유자는 [Azure Portal](https://portal.azure.com)의 스토리지 계정에 대한 ‘*프라이빗 엔드포인트*’ 탭을 통해 동의 요청 및 프라이빗 엔드포인트를 관리할 수 있습니다.

> [!TIP]
> 프라이빗 엔드포인트를 통해서만 스토리지 계정에 액세스 가능하도록 제한하려는 경우 퍼블릭 엔드포인트를 통해 액세스를 거부하거나 제어하도록 스토리지 방화벽을 구성합니다.

기본적으로 퍼블릭 엔드포인트를 통해 액세스를 거부하도록 [스토리지 방화벽을 구성](storage-network-security.md#change-the-default-network-access-rule)하여 VNet의 연결만 허용하도록 스토리지 계정을 보호할 수 있습니다. 스토리지 방화벽은 퍼블릭 엔드포인트를 통해서만 액세스를 제어하므로 프라이빗 엔드포인트가 있는 VNet의 트래픽을 허용하는 방화벽 규칙이 불필요합니다. 대신 프라이빗 엔드포인트는 서브넷에 스토리지 서비스에 대한 액세스 권한을 부여하는 동의 플로우를 사용합니다.

> [!NOTE]
> 스토리지 계정 간에 BLOB을 복사하는 경우 클라이언트에 두 계정 모두에 대한 네트워크 액세스 권한이 있어야 합니다. 따라서 하나의 계정(원본 또는 대상)에 대해서만 프라이빗 링크를 사용하도록 선택하는 경우 클라이언트에 다른 계정에 대한 네트워크 액세스 권한이 있는지 확인합니다. 네트워크 액세스를 구성하는 다른 방법에 대해 알아보려면 [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md?toc=/azure/storage/blobs/toc.json)을 참조하세요.

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

Azure Portal을 사용해 프라이빗 엔드포인트를 만들려면 [Azure Portal의 스토리지 계정 환경에서 스토리지 계정에 프라이빗 방식으로 연결](../../private-link/tutorial-private-endpoint-storage-portal.md)을 참조하세요.

PowerShell 또는 Azure CLI를 사용하여 프라이빗 엔드포인트를 만들려면 해당 문서 중 하나를 참조하세요. 둘 다 Azure 웹앱을 대상 서비스로 기능하게 하지만 프라이빗 링크를 만드는 단계는 Azure Storage 계정에서 수행하는 단계와 동일합니다.

- [Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-cli.md)

- [Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-powershell.md)

프라이빗 엔드포인트를 만들 때 해당 프라이빗 엔드포인트를 연결하는 스토리지 계정 및 스토리지 서비스를 지정해야 합니다.

액세스해야 하는 각 스토리지 리소스(즉, [BLOB](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [파일](../files/storage-files-introduction.md), [큐](../queues/storage-queues-introduction.md), [테이블](../tables/table-storage-overview.md)또는 [정적 웹 사이트](../blobs/storage-blob-static-website.md))에 대한 별도의 프라이빗 엔드포인트가 필요합니다. 프라이빗 엔드포인트에서 해당 스토리지 서비스는 연결된 스토리지 계정의 **대상 하위 리소스** 로 정의됩니다.

Data Lake Storage Gen2 스토리지 리소스에 대한 프라이빗 엔드포인트를 만드는 경우 Blob 스토리지 리소스에 대해 하나의 엔드포인트를 만들어야 합니다. Data Lake Storage Gen2 엔드포인트를 대상으로 하는 작업이 Blob 엔드포인트로 리디렉션되기 때문입니다. 두 리소스에 대한 프라이빗 엔드포인트를 만들어 작업을 성공적으로 완료할 수 있는지 확인합니다.

> [!TIP]
> RA-GRS 계정 읽기 성능을 향상시키기 위해 스토리지 서비스의 보조 인스턴스에 대한 별도의 프라이빗 엔드포인트를 만듭니다.
> 범용 v2(표준 혹은 프리미엄) 스토리지 계정을 만들어야 합니다.

지역 중복 스토리지용으로 구성된 스토리지 계정을 사용한 보조 지역 대상 읽기 권한의 경우 서비스의 주 및 보조 인스턴스에 별도의 프라이빗 엔드포인트를 사용해야 합니다. **장애 조치(failover)** 를 위해 보조 인스턴스의 프라이빗 엔드포인트를 만들 필요는 없습니다. 장애 조치(failover) 후 프라이빗 엔드포인트는 새로운 주 인스턴스에 자동으로 연결됩니다. Azure Storage 중복 옵션에 대한 자세한 내용은 [Azure Storage 중복](storage-redundancy.md)을 참조하세요.

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>프라이빗 엔드포인트에 연결

프라이빗 엔드포인트를 사용하는 VNet의 클라이언트는 퍼블릭 엔드포인트에 연결하는 클라이언트와 동일한 스토리지 계정 연결 문자열을 사용해야 합니다. DNS 확인에 의존하여 프라이빗 링크를 통해 VNet에서 스토리지 계정으로 연결을 자동으로 라우팅합니다.

> [!IMPORTANT]
> 다른 방법으로는 동일한 연결 문자열을 사용하여 프라이빗 엔드포인트를 통해 스토리지 계정에 연결합니다. `privatelink` 하위 도메인 URL을 사용해서 스토리지 계정에 연결하지 마세요.

기본적으로 프라이빗 엔드포인트에 대한 필수 업데이트를 사용하여 VNet에 연결된 [프라이빗 DNS 영역](../../dns/private-dns-overview.md)을 만듭니다. 그러나 사용자가 자체 DNS 서버를 사용하는 경우 DNS 구성을 추가로 변경해야 할 수 있습니다. 아래 [DNS 변경](#dns-changes-for-private-endpoints) 섹션에서는 프라이빗 엔드포인트에 필요한 업데이트에 대해 설명합니다.

## <a name="dns-changes-for-private-endpoints"></a>프라이빗 엔드포인트용 DNS 변경

프라이빗 엔드포인트를 만들 때 스토리지 계정에 대한 DNS CNAME 리소스 레코드는 `privatelink` 접두사가 있는 하위 도메인의 별칭으로 업데이트됩니다. 또한 기본적으로 프라이빗 엔드포인트에 대한 DNS A 리소스 레코드를 사용하여 `privatelink` 하위 도메인에 해당하는 [프라이빗 DNS 영역](../../dns/private-dns-overview.md)을 만듭니다.

프라이빗 엔드포인트를 사용하여 VNet 외부에서 스토리지 엔드포인트 URL을 확인하면 스토리지 서비스의 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 호스트하는 VNet에서 확인되면 스토리지 엔드포인트 URL은 프라이빗 엔드포인트의 IP 주소로 확인됩니다.

위에 설명된 예시에서 프라이빗 엔드포인트를 호스트하는 VNet 외부에서 확인되는 경우 스토리지 계정 ‘StorageAccountA’에 대한 DNS 리소스 레코드는 다음과 같습니다.

| 이름                                                  | 유형  | 값                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| `StorageAccountA.blob.core.windows.net`             | CNAME | `StorageAccountA.privatelink.blob.core.windows.net` |
| `StorageAccountA.privatelink.blob.core.windows.net` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

앞에서 설명했듯이 스토리지 방화벽을 사용하여 퍼블릭 엔드포인트를 통해 VNet 외부의 클라이언트에 대한 액세스를 거부하거나 제어할 수 있습니다.

StorageAccountA에 대한 DNS 리소스 레코드는 프라이빗 엔드포인트를 호스트하는 VNet의 클라이언트에서 확인되는 경우 다음과 같습니다.

| 이름  | 유형 | 값 |
| :--- | :---: | :--- |
| `StorageAccountA.blob.core.windows.net` | CNAME | `StorageAccountA.privatelink.blob.core.windows.net` |
| `StorageAccountA.privatelink.blob.core.windows.net` | A | `10.1.1.5` |

이 방법을 사용하면 프라이빗 엔드포인트를 호스트하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 **동일한 연결 문자열을 사용** 하여 스토리지 계정에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 프라이빗 엔드포인트 IP 주소에 대한 스토리지 계정 엔드포인트의 FQDN을 확인할 수 있어야 합니다. 프라이빗 링크 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하도록 DNS 서버를 구성하거나 프라이빗 엔드포인트 IP 주소를 사용하여 `StorageAccountA.privatelink.blob.core.windows.net`에 대한 A 레코드를 구성해야 합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용하는 경우 `privatelink` 하위 도메인의 스토리지 계정 이름을 프라이빗 엔드포인트 IP 주소로 확인하도록 DNS 서버를 구성해야 합니다. `privatelink` 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하거나 DNS 서버에서 DNS 영역을 구성하고 DNS A 레코드를 추가하여 이를 수행할 수 있습니다.

스토리지 서비스에 대한 프라이빗 엔드포인트와 연결된 엔드포인트 대상 하위 리소스에 권장되는 DNS 영역 이름은 다음과 같습니다.

| 스토리지 서비스        | 대상 하위 리소스 | 영역 이름                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Blob service           | blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | dfs                 | `privatelink.dfs.core.windows.net`   |
| 파일 서비스           | 파일                | `privatelink.file.core.windows.net`  |
| 큐 서비스          | queue               | `privatelink.queue.core.windows.net` |
| 테이블 서비스          | 테이블               | `privatelink.table.core.windows.net` |
| 정적 웹 사이트        | web                 | `privatelink.web.core.windows.net`   |

프라이빗 엔드포인트 지원 목적으로 자체 DNS 서버를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 가상 네트워크의 리소스 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [프라이빗 엔드포인트에 대한 DNS 구성](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

Azure Storage용 프라이빗 엔드포인트에 관련된 다음과 같은 알려진 문제를 염두에 두어야 합니다.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 VNet의 클라이언트에 대한 스토리지 액세스 제약 조건

기존 프라이빗 엔드포인트를 사용하는 VNet의 클라이언트는 프라이빗 엔드포인트가 있는 다른 스토리지 계정에 액세스할 때 제약 조건이 걸립니다. 예를 들어 VNet N1에 Blob 스토리지용 스토리지 계정 A1에 대한 프라이빗 엔드포인트가 있다고 가정합니다. 스토리지 계정 A2에 Blob 스토리지용 VNet N2의 프라이빗 엔드포인트가 있는 경우 VNet N1의 클라이언트는 프라이빗 엔드포인트를 사용해 A2 계정에 있는 Blob 스토리지에도 액세스해야 합니다. 스토리지 계정 A2에 Blob 스토리지용 프라이빗 엔드포인트가 없는 경우 VNet N1의 클라이언트는 프라이빗 엔드포인트 없이 해당 계정의 Blob 스토리지에 액세스할 수 있습니다.

이 제약 조건은 계정 A2가 프라이빗 엔드포인트를 만들 때 적용되는 DNS 변경의 결과입니다.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 서브넷의 네트워크 보안 그룹 규칙

현재 프라이빗 엔드포인트에 대한 NSG([네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md)) 규칙 및 사용자 정의 경로를 구성할 수 없습니다. 프라이빗 엔드포인트를 호스트하는 서브넷에 적용된 NSG 규칙은 프라이빗 엔드포인트에 적용되지 않습니다. 다른 엔드포인트(예: 네트워크 인터페이스 컨트롤러)에만 적용됩니다. 이 문제를 부분적으로 해결하는 방법은 원본 서브넷의 프라이빗 엔드포인트에 대한 액세스 규칙 구현입니다. 단, 이 방법을 쓰면 더 높은 관리 오버헤드가 필요할 수 있습니다.

### <a name="copying-blobs-between-storage-accounts"></a>스토리지 계정 간에 Blob 복사

Azure REST API 또는 REST API를 사용하는 도구를 사용하는 경우에만 프라이빗 엔드포인트를 통해 스토리지 계정 간에 Blob을 복사할 수 있습니다. 해당 도구에는 AzCopy, Storage 탐색기, Azure PowerShell, Azure CLI 및 Azure Blob Storage SDK가 있습니다.

Blob 스토리지 리소스를 대상으로 하는 프라이빗 엔드포인트만 지원됩니다. Data Lake Storage Gen2 또는 파일 리소스를 대상으로 하는 프라이빗 엔드포인트는 아직 지원되지 않습니다. 또한 NFS(네트워크 파일 시스템) 프로토콜을 사용한 스토리지 계정 간의 복사는 아직 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md)
- [Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
