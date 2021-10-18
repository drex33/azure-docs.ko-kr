---
title: 프라이빗 엔드포인트에 대한 DNS 이름 확인 구성
description: 이 문서에서는 Purview 계정에 대한 프라이빗 엔드포인트를 사용하는 방법을 간략하게 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: 95d14afebd6567e18aed6e4585c930a3d46ccb42
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137696"
---
# <a name="configure-and-verify-dns-name-resolution-for-azure-purview-private-endpoints"></a>Azure Purview 프라이빗 엔드포인트에 대한 DNS 이름 확인 구성 및 확인

> [!IMPORTANT]
> **2021년 9월 27일 15:30 UTC 이전에** Purview 계정에 대한 _포털_ 프라이빗 엔드포인트를 만든 경우 [포털 프라이빗 엔드포인트에 대한 DNS 다시 구성에](./catalog-private-link.md#reconfigure-dns-for-portal-private-endpoints)설명된 대로 필요한 작업을 수행해야 합니다. **이러한 작업은 2021년 10월 11일 이전에 완료해야 합니다. 이렇게 하지 않으면 기존 포털 프라이빗 엔드포인트의 작동이 중지됩니다.**

## <a name="conceptual-overview"></a>개념적 개요
정확한 이름 확인은 Azure Purview 계정에 대한 프라이빗 엔드포인트를 설정하는 경우 중요한 요구 사항입니다. 

데이터 원본 및 관리 컴퓨터에서 Azure Purview 계정 및 자체 호스팅 통합 런타임으로 프라이빗 엔드포인트 IP 주소를 FQDN(정규화된 도메인 이름)으로 확인하려면 배포하는 시나리오에 따라 DNS 설정에서 내부 이름 확인을 사용하도록 설정해야 할 수 있습니다.

다음 예에서는 가상 네트워크 외부에서 또는 Azure 프라이빗 엔드포인트가 구성되지 않은 경우의 Azure Purview DNS 이름 확인을 보여줍니다.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="CorpNet 외부에서 Azure Purview 이름 확인을 보여주는 스크린샷":::

다음 예제에서는 가상 네트워크 내에서 Azure Purview DNS 이름 확인을 보여줍니다.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="CorpNet 내부에서 Purview 이름 확인을 보여주는 스크린샷":::

## <a name="deployment-options"></a>배포 옵션 

Azure Purview 계정에 프라이빗 엔드포인트를 사용할 때 내부 이름 확인 설정을 위해 다음 옵션 중 일부를 사용합니다.

- 프라이빗 엔드포인트 배포의 Azure 환경 부분에 [새 Azure 개인 DNS 영역을 배포](#option-1---deploy-new-azure-private-dns-zones)합니다. (기본 옵션)
- [기존 Azure 개인 DNS 영역을 사용](#option-2---use-existing-azure-private-dns-zones)합니다. 다른 구독이나 동일한 구독 내에서도 허브 및 스포크 모델의 프라이빗 엔드포인트를 사용하는 경우 이 옵션을 사용합니다. 
- DNS 전달자를 사용하지 않고 대신 온-프레미스 DNS 서버에서 직접 A 레코드를 관리하는 경우에는 [자체 DNS 서버를 사용](#option-3---use-your-own-dns-servers)합니다.

## <a name="option-1---deploy-new-azure-private-dns-zones"></a>옵션 1 - 새 Azure 개인 DNS 영역 배포  

### <a name="deploy-new-azure-private-dns-zones"></a>새 Azure 프라이빗 DNS 영역 배포
내부 이름 확인을 사용하도록 설정하려면 Azure Purview 계정이 배포된 Azure 구독 내에 필요한 Azure DNS 영역을 배포하면 됩니다. 

   :::image type="content" source="media/catalog-private-link/purview-pe-dns-zones.png" alt-text="DNS 영역을 보여 주는 스크린샷":::

검색, 포털 및 계정 프라이빗 엔드포인트를 만들 때 Azure Purview에 대한 DNS CNAME 리소스 레코드는 접두사 를 사용하여 일부 하위 도메인의 별칭으로 자동으로 업데이트됩니다. `privatelink`

- 기본적으로 Purview 계정에 대한 _계정_ 프라이빗 엔드포인트를 배포하는 동안 프라이빗 엔드포인트에 대한 DNS A 리소스 레코드를 포함하여 Azure Purview의 하위 도메인에 해당하는 프라이빗 DNS [영역도](../dns/private-dns-overview.md) `privatelink` `privatelink.purview.azure.com` 만듭니다.

- Purview 계정에 대한 _포털_ 프라이빗 엔드포인트를 배포하는 동안 웹에 대한 DNS `privatelink` A 리소스 레코드를 포함하여 Azure Purview의 하위 도메인에 해당하는 새 프라이빗 DNS 영역도 `privatelink.purviewstudio.azure.com` 만듭니다. 

- 수집 프라이빗 엔드포인트를 사용하도록 설정하면 관리되는 리소스에 추가 DNS 영역이 필요합니다. 

다음 표에서는 배포 중에  _DNS 통합_ 을 사용하도록 설정하는 경우, Azure Purview 계정에 대한 프라이빗 엔드포인트 구성의 일부로 배포되는 Azure 개인 DNS 영역 및 DNS A 레코드의 예를 보여줍니다. 

프라이빗 엔드포인트  |연결된 프라이빗 엔드포인트  |DNS 영역(신규)  |레코드(예제) |
|---------|---------|---------|---------|
|계정     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
|포털     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |웹         |
|수집     |Purview 관리형 스토리지 계정 - Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|수집   |Purview 관리형 스토리지 계정 - 큐         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|수집     |Purview 관리형 스토리지 계정 - Event Hub         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

### <a name="validate-virtual-network-links-on-azure-private-dns-zones"></a>Azure 프라이빗 DNS 영역에서 가상 네트워크 링크 유효성 검사

프라이빗 엔드포인트 배포가 완료되면 프라이빗 엔드포인트가 배포된 Azure 가상 네트워크에 해당하는 모든 Azure 프라이빗 DNS 영역에 가상 네트워크 [링크가](../dns/private-dns-virtual-network-links.md) 있는지 확인합니다.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-link.png" alt-text="DNS 영역의 가상 네트워크 링크를 보여 주는 스크린샷.":::

자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md)을 참조하세요.

### <a name="verify-internal-name-resolution"></a>내부 이름 확인 확인

프라이빗 엔드포인트를 사용하여 가상 네트워크 외부에서 Azure Purview 엔드포인트 URL을 확인하는 경우 Azure Purview의 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 호스트하는 가상 네트워크에서 확인하는 경우 Azure Purview 엔드포인트 URL은 프라이빗 엔드포인트의 IP 주소로 확인됩니다.

예를 들어 Azure Purview 계정 이름이 'Contoso-Purview'인 경우 프라이빗 엔드포인트를 호스트하는 가상 네트워크 외부에서 확인되면 다음과 같습니다.

| Name | 유형 | 값 |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview Studio public endpoint\> |

프라이빗 엔드포인트를 호스트하는 가상 네트워크에서 확인되는 경우 Contoso-Purview에 대한 DNS 리소스 레코드는 다음과 같습니다.

| Name | 유형 | 값 |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | A | \<Purview account private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview portal private endpoint IP address\> |

## <a name="option-2---use-existing-azure-private-dns-zones"></a>옵션 2 - 기존 Azure 개인 DNS 영역 사용

### <a name="use-existing-azure-private-dns-zones"></a>기존 Azure 프라이빗 DNS 영역 사용

Azure purview 프라이빗 엔드포인트를 배포하는 동안 기존 Azure 프라이빗 DNS 영역을 사용하여 _프라이빗 DNS 통합을_ 선택할 수 있습니다. 이는 프라이빗 엔드포인트가 Azure의 다른 서비스에 사용되는 조직의 일반적인 경우입니다. 이 경우 프라이빗 엔드포인트를 배포하는 동안 새 DNS 영역을 만드는 대신 기존 DNS 영역을 선택해야 합니다. 

이 시나리오는 조직에서 모든 Azure 프라이빗 DNS 영역에 중앙 또는 허브 구독을 사용하는 경우에도 적용됩니다.

다음 목록에서는 Purview 프라이빗 엔드포인트에 필요한 Azure DNS 영역 및 A 레코드를 보여줍니다.

> [!NOTE]
> `Contoso-Purview`, `scaneastusabcd1234` 및 `atlas-12345678-1234-1234-abcd-123456789abc`를 사용해 모든 이름을 사용자 환경의 해당 Azure 리소스 이름으로 업데이트합니다. 예를 들어 `scaneastusabcd1234` 대신 Azure Purview 관리형 스토리지 계정의 이름을 사용합니다.

프라이빗 엔드포인트  |연결된 프라이빗 엔드포인트  |DNS 영역(기존)  |레코드(예제) |
|---------|---------|---------|---------|
|계정     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
|포털     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |웹         |
|수집     |Purview 관리형 스토리지 계정 - Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|수집   |Purview 관리형 스토리지 계정 - 큐         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|수집     |Purview 관리형 스토리지 계정 - Event Hub         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Azure Purview 이름 확인을 보여주는 다이어그램"lightbox="media/catalog-private-link/purview-name-resolution-diagram.png":::

자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md)에서 [사용자 지정 DNS 서버가 없는 가상 네트워크 워크로드](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) 및 [DNS 전달자를 사용하는 온-프레미스 워크로드](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) 시나리오를 참조하세요.

### <a name="verify-virtual-network-links-on-azure-private-dns-zones"></a>Azure 프라이빗 DNS 영역에서 가상 네트워크 링크 확인

개인 끝점 배포가 완료 되 면 해당 하는 모든 Azure 사설 DNS 영역에 개인 끝점이 배포 된 Azure virtual network에 대 한 [가상 네트워크 링크가](../dns/private-dns-virtual-network-links.md) 있는지 확인 합니다.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-link.png" alt-text="DNS 영역에 대 한 가상 네트워크 링크를 보여 주는 스크린샷":::

자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md)을 참조하세요.

### <a name="configure-dns-forwarders-if-custom-dns-is-used"></a>사용자 지정 DNS를 사용 하는 경우 DNS 전달자 구성

또한 자체 호스팅 integration runtime VM 또는 관리 PC가 있는 Azure virtual network에서 DNS 구성의 유효성을 검사 해야 합니다. 

   :::image type="content" source="media/catalog-private-link/purview-pe-custom-dns.png" alt-text="Azure 가상 네트워크 사용자 지정 DNS를 보여 주는 다이어그램":::

- _기본값으로_ 구성 된 경우에는이 단계에서 추가 작업이 필요 하지 않습니다.

-  사용자 지정 DNS 서버를 사용 하는 경우 다음 영역에 대 한 DNS 서버 내에 해당 DNS 전달자를 추가 해야 합니다.
  
   -  Purview.azure.com
   -  Blob.core.windows.net
   -  Queue.core.windows.net
   -  Servicebus.windows.net

### <a name="verify-internal-name-resolution"></a>내부 이름 확인 확인

프라이빗 엔드포인트를 사용하여 가상 네트워크 외부에서 Azure Purview 엔드포인트 URL을 확인하는 경우 Azure Purview의 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 호스트하는 가상 네트워크에서 확인하는 경우 Azure Purview 엔드포인트 URL은 프라이빗 엔드포인트의 IP 주소로 확인됩니다.

예를 들어, Azure 부서의 범위 계정 이름이 ' Contoso-부서의 범위 ' 인 경우 개인 끝점을 호스트 하는 가상 네트워크 외부에서 확인 되 면 다음과 같이 됩니다.

| Name | 유형 | 값 |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview Studio public endpoint\> |

개인 끝점을 호스트 하는 가상 네트워크에서 확인 된 경우 Contoso-부서의 범위에 대 한 DNS 리소스 레코드는 다음과 같습니다.

| Name | 유형 | 값 |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | A | \<Purview account private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview portal private endpoint IP address\> |

## <a name="option-3---use-your-own-dns-servers"></a>옵션 3 - 자체 DNS 서버 사용

DNS 전달자를 사용하지 않고 대신 온-프레미스 DNS 서버에서 직접 A 레코드를 관리하여 개인 IP 주소를 통해 엔드포인트를 확인하는 경우, DNS 서버에서 다음 A 레코드를 만들어야 할 수도 있습니다.

> [!NOTE]
> `Contoso-Purview`, `scaneastusabcd1234` 및 `atlas-12345678-1234-1234-abcd-123456789abc`를 사용해 모든 이름을 사용자 환경의 해당 Azure 리소스 이름으로 업데이트합니다. 예를 들어 `scaneastusabcd1234` 대신 Azure Purview 관리형 스토리지 계정의 이름을 사용합니다.

| Name | 유형 | 값 |
| ---------- | -------- | --------------- |
| `web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.blob.core.windows.net` | A | \<blob-ingestion private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.queue.core.windows.net` | A | \<queue-ingestion private endpoint IP address of Azure Purview> |
| `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`| A | \<namespace-ingestion private endpoint IP address of Azure Purview> |
| `Contoso-Purview.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `Contoso-Purview.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `Contoso-Purview.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `gateway.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |


## <a name="verify-and-dns-test-name-resolution-and-connectivity"></a>DNS 테스트 이름 확인 및 연결 확인 

1. Azure 개인 DNS 영역을 사용하는 경우 다음 DNS 영역 및 해당 A 레코드가 Azure 구독에 만들어졌는지 확인합니다.

   |프라이빗 엔드포인트  |연결된 프라이빗 엔드포인트  |DNS 영역  |레코드(예제) |
   |---------|---------|---------|---------|
   |계정     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
   |포털     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |웹         |
   |수집     |Purview 관리형 스토리지 계정 - Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
   |수집   |Purview 관리형 스토리지 계정 - 큐         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
   |수집     |Purview 관리형 스토리지 계정 - Event Hub         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

2. 내부 이름 확인을 허용하도록 Azure Virtual Network에 대한 Azure 개인 DNS 영역에[가상 네트워크 링크](../dns/private-dns-virtual-network-links.md)를 만듭니다.
   
3. 관리 PC 및 자체 호스팅 통합 런타임 VM에서 Nslookup.exe 및 PowerShell과 같은 도구를 사용하여 Azure Purview 계정에 대한 이름 확인 및 네트워크 연결을 테스트합니다.

이름 확인을 테스트 하려면 개인 IP 주소를 통해 다음 Fqdn을 확인 해야 합니다. (Contoso-부서의 범위, scaneastusabcd1234 또는 atlas-12345678-1234-1234-123456789abc 대신 부서의 범위 계정 이름 및 관리 되는 리소스 이름과 연결 된 호스트 이름 사용)

- `Contoso-Purview.purview.azure.com`
- `web.purview.azure.com`
- `scaneastusabcd1234.blob.core.windows.net`
- `scaneastusabcd1234.queue.core.windows.net`
- `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`

자체 호스팅 통합 런타임 VM에서 네트워크 연결을 테스트하려면 `Test-NetConnection`을 사용하여 PowerShell 콘솔을 시작하고 연결을 테스트하면 됩니다. 각 엔드포인트를 프라이빗 엔드포인트로 확인하고 TcpTestSucceeded를 True로 가져와야 합니다. (Contoso-부서의 범위, scaneastusabcd1234 또는 atlas-12345678-1234-1234-123456789abc 대신 부서의 범위 계정 이름 및 관리 되는 리소스 이름과 연결 된 호스트 이름을 사용 합니다.)

- `Test-NetConnection -ComputerName Contoso-Purview.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName web.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.blob.core.windows.net -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.queue.core.windows.net -port 443`
- `Test-NetConnection -ComputerName atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net -port 443` 

## <a name="next-steps"></a>다음 단계

- [Azure Purview 계정에 대한 프라이빗 엔드포인트 구성 문제 해결](catalog-private-link-troubleshoot.md)
- [Azure Purview에서 데이터 원본 관리](./manage-data-sources.md)
