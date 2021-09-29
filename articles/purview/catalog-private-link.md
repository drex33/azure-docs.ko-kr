---
title: Purview에 대한 보안 액세스를 위해 프라이빗 엔드포인트 사용
description: 이 문서에서는 Purview 계정에 대한 프라이빗 엔드포인트를 사용하는 방법을 간략하게 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 925c556ccc5657af604eb80d5d697ed6ebcb5260
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210652"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Azure Purview 계정에 대해 프라이빗 엔드포인트 사용

> [!IMPORTANT]
> **2021년 9월 27일 15:30 UTC 이전에** Purview 계정에 대한 _포털_ 프라이빗 엔드포인트를 만든 경우 [포털 프라이빗 엔드포인트에 대한 DNS 다시 구성에](#reconfigure-dns-for-portal-private-endpoints)설명된 대로 필요한 작업을 수행해야 합니다. **이러한 작업은 2021년 10월 11일 이전에 완료해야 합니다. 이렇게 하지 않으면 기존 포털 프라이빗 엔드포인트의 작동이 중지됩니다.**


이 문서에서는 Azure Purview에 대해 프라이빗 엔드포인트를 구성하는 방법을 설명합니다.

## <a name="conceptual-overview"></a>개념적 개요
Azure Purview 계정에 대해 [Azure 프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 VNet(가상 네트워크)의 사용자가 프라이빗 링크를 통해 카탈로그에 안전하게 액세스할 수 있도록 할 수 있습니다. 프라이빗 엔드포인트는 Purview 계정에 대해 VNet 주소 공간의 IP 주소를 사용합니다. VNet에 있는 사용자와 Purview 계정 사이의 네트워크 트래픽은 VNet과 Microsoft 백본 네트워크의 프라이빗 링크를 가로지릅니다. 

프라이빗 네트워크 내에서 시작된 Azure Purview에 대한 클라이언트 호출만 허용하도록 Azure Purview _계정_ 프라이빗 엔드포인트를 배포할 수 있습니다.

프라이빗 네트워크 연결을 사용하여 Azure Purview Studio에 연결하려면 _포털_ 프라이빗 엔드포인트를 배포할 수 있습니다.

프라이빗 연결을 통해 Azure 가상 네트워크 및 온-프레미스 데이터 원본 내에서 Azure IaaS 및 PaaS 데이터 원본을 검사해야 하는 경우 _수집_ 프라이빗 엔드포인트를 배포할 수 있습니다. 이 방법은 데이터 원본에서 Azure Purview 데이터 맵으로 이동하는 메타데이터의 네트워크 격리를 보장합니다.

:::image type="content" source="media/catalog-private-link/purview-private-link-overview.png" alt-text="프라이빗 엔드포인트가 있는 Azure Purview를 보여 주는 스크린샷"::: 

## <a name="prerequisites"></a>필수 조건

Azure Purview 계정에 대한 프라이빗 엔드포인트를 배포하기 전에 다음 사전 요구 사항을 충족하는지 확인합니다.

1. 활성 구독이 있는 Azure 계정. [체험 계정을 만드세요.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
<br>
2. 기존 Azure Virtual Network. [Azure 가상 네트워크](../virtual-network/quick-create-portal.md)가 없으면 새로 배포합니다.
<br>

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Azure Purview 프라이빗 엔드포인트 배포 시나리오

다음 권장 검사 목록을 사용하여 프라이빗 엔드포인트의 Azure Purview 계정 배포를 수행합니다.

|시나리오  |목표  |
|---------|---------|
|**시나리오 1**  -  [Azure Purview에 커넥트 데이터 원본을 비공개 및 안전하게 검사](./catalog-private-link-end-to-end.md) |Azure Purview Studio, Atlas API에 대한 액세스를 포함하여 프라이빗 엔드포인트를 통해서만 Azure Purview 계정에 대한 액세스를 제한하고, 자체 호스팅 통합 런타임을 사용하여 온-프레미스 및 가상 네트워크 뒤의 Azure에서 데이터 원본을 검사하여 엔드투엔드 네트워크 격리를 보장해야 합니다. (_계정_, _포털_ 및 _수집_ 프라이빗 엔드포인트 배포)   |
|**시나리오 2**  -  [Purview 계정에 비공개 및 안전하게 커넥트](./catalog-private-link-account-portal.md)   | 프라이빗 엔드포인트를 통해 _Azure Purview Studio_ 및 Atlas API에 대한 액세스를 비롯하여 Azure Purview 계정에 대한 액세스를 사용하도록 설정해야 합니다. (_계정_ 및 _포털_ 프라이빗 엔드포인트 배포)   |

## <a name="support-matrix-for-scanning-data-sources-through-_ingestion_-private-endpoint"></a>_수집_ 프라이빗 엔드포인트를 통한 데이터 원본 검사 관련 지원 매트릭스

Azure Purview 계정에서 _수집_ 프라이빗 엔드포인트를 사용하고 데이터 원본에 대한 퍼블릭 액세스를 사용하지 않도록 설정한 시나리오의 경우 Azure Purview는 프라이빗 엔드포인트 뒤에 있는 다음 데이터 원본을 검사할 수 있습니다.

|프라이빗 엔드포인트 뒤의 데이터 원본  |통합 런타임 유형  |자격 증명 유형  |
|---------|---------|---------|
|Azure Blob Storage | 자체 호스팅 IR | 서비스 주체|
|Azure Blob Storage | 자체 호스팅 IR | 계정 키|
|Azure Data Lake Storage Gen 2 | 자체 호스팅 IR| 서비스 주체|
|Azure Data Lake Storage Gen 2 | 자체 호스팅 IR| 계정 키|
|Azure SQL Database | 자체 호스팅 IR| SQL 인증|
|Azure SQL Database | 자체 호스팅 IR| 서비스 주체|
|Azure SQL Managed Instance | 자체 호스팅 IR| SQL 인증|
|Azure Cosmos DB| 자체 호스팅 IR| 계정 키|
|SQL Server | 자체 호스팅 IR| SQL 인증|
|Azure Synapse Analytics | 자체 호스팅 IR| 서비스 주체|
|Azure Synapse Analytics | 자체 호스팅 IR| SQL 인증|

## <a name="reconfigure-dns-for-portal-private-endpoints"></a>포털 프라이빗 엔드포인트에 대한 DNS 다시 구성

**2021년 9월 27일(UTC) 15:30 이전에** Purview 계정에 대한 _포털_ 프라이빗 엔드포인트를 만든 경우 이 섹션에 설명된 대로 필요한 작업을 수행합니다.

- **사용자 지정 DNS를 사용하거나 필요한 DNS A 레코드를** 컴퓨터의 호스트 파일에 직접 추가하는 경우 **아무 작업도 필요하지 않습니다.** 

- Purview 계정에 **Azure 프라이빗 DNS 영역 통합을 구성한** 경우 다음 단계에 따라 프라이빗 엔드포인트를 다시 배포하여 DNS 설정을 다시 구성합니다. 

    1. 새 포털 프라이빗 엔드포인트 배포:
       
        1. [Azure Portal](https://portal.azure.com)로 이동한 다음 Azure Purview 계정을 클릭하고 **설정** 에서 **네트워킹** 을 선택한 다음 **프라이빗 엔드포인트 연결** 을 선택합니다.

            :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal.png" alt-text="포털 프라이빗 엔드포인트 만들기를 보여 주는 스크린샷.":::

        2. **+ 프라이빗 엔드포인트** 를 선택하여 새 프라이빗 엔드포인트를 만듭니다.

        3. 기본 정보를 입력합니다.

        4. **리소스** 탭에서 **리소스 종류** 에 대해 **Microsoft.Purview/portal을** 선택합니다.

        5. **리소스에** 대해 Azure Purview 계정을 선택하고 대상 하위 리소스 에 **대해 포털** **을** 선택합니다.

        6. **구성** 탭에서 가상 네트워크를 선택한 다음, Azure 프라이빗 DNS 영역을 선택하여 새 Azure DNS 영역을 만듭니다.
            
            :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal-dns.png" alt-text="포털 프라이빗 엔드포인트 및 DNS 설정 만들기를 보여 주는 스크린샷.":::

        7. 요약 페이지로 이동하고 **만들기** 를 선택하여 포털 프라이빗 엔드포인트를 만듭니다.

    2. Purview 계정과 연결된 이전 포털 프라이빗 엔드포인트를 삭제합니다. 

    3. 포털 프라이빗 엔드포인트를 배포하는 동안 새 Azure 프라이빗 DNS 영역(privatelink.purviewstudio.azure.com)이 생성되고 해당 A 레코드(웹)가 프라이빗 DNS 영역에 있는지 확인합니다. 
    
    4. Azure Purview Studio를 성공적으로 로드할 수 있는지 확인합니다. DNS를 다시 구성한 후 새 DNS 라우팅이 적용되는 데 몇 분(약 10분)이 걸릴 수 있습니다. 즉시 로드되지 않으면 몇 분 정도 기다렸다가 다시 시도할 수 있습니다.
    
    5. 탐색이 실패하면 포털 프라이빗 엔드포인트에 연결된 개인 IP 주소로 확인되어야 하는 nslookup web.purview.azure.com 수행합니다.
  
    6. 있는 모든 기존 포털 프라이빗 엔드포인트에 대해 위의 1~3단계를 반복합니다. 

- **온-프레미스 또는 사용자 지정 DNS** 확인(예: 자체 DNS 서버를 사용 중이거나 호스트 파일을 구성한 경우)을 구성한 경우 다음 작업을 수행합니다. 

  - DNS 레코드가 `web.purview.azure.com` 이면 **아무 작업도 필요하지 않습니다.**  
  - DNS 레코드가 `web.privatelink.purview.azure.com` 이면 레코드를 로 `web.privatelink.purviewstudio.azure.com` 업데이트합니다. 

## <a name="frequently-asked-questions"></a>질문과 대답  

Azure Purview의 프라이빗 엔드포인트 배포와 관련된 FAQ는 [Azure Purview 프라이빗 엔드포인트에 대한 FAQ](./catalog-private-link-faqs.md)를 참조하세요.
 
## <a name="troubleshooting-guide"></a>문제 해결 가이드 
Purview 계정에 대한 프라이빗 엔드포인트 구성 문제를 해결하려면 [Purview 계정에 대한 프라이빗 엔드포인트 구성 문제 해결](./catalog-private-link-troubleshoot.md)을 참조하세요.

## <a name="known-limitations"></a>알려진 제한 사항
Azure Purview 프라이빗 엔드포인트와 관련된 현재 제한 사항 목록을 보려면 [Azure Purview 프라이빗 엔드포인트 알려진 제한 사항](./catalog-private-link-troubleshoot.md#known-limitations)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [종단 간 개인 네트워킹 배포](./catalog-private-link-end-to-end.md)
- [부서의 범위 Studio에 대 한 개인 네트워킹 배포](./catalog-private-link-account-portal.md)
