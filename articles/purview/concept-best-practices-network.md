---
title: Azure 부서의 범위 네트워크 아키텍처 및 모범 사례
description: 이 문서에서는 Azure 부서의 범위 네트워크 아키텍처 옵션의 예제를 제공 하 고 모범 사례를 설명 합니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 04121992c3de5e061e54687c8d23469850607870
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073984"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Azure 부서의 범위 네트워크 아키텍처 및 모범 사례

Azure 부서의 범위는 데이터 관리를 위한 PaaS (platform as a service) 솔루션입니다. Azure 부서의 범위 계정에는 서비스에 연결 하기 위해 인터넷을 통해 액세스할 수 있는 공용 끝점이 있습니다. 그러나 모든 끝점은 Azure Active Directory (Azure AD) 로그인 및 RBAC (역할 기반 액세스 제어)를 통해 보안이 유지 됩니다.

추가 보안 계층의 경우 Azure 부서의 범위 계정에 대 한 개인 끝점을 만들 수 있습니다. 그런 다음 Azure의 가상 네트워크에서 Azure 부서의 범위 계정 및 관리 되는 리소스에 대 한 개인 IP 주소를 가져옵니다. 이 주소를 사용 하면 가상 네트워크와 Azure 부서의 범위 계정 간의 모든 트래픽이 Api 및 Azure 부서의 범위 Studio와의 사용자 상호 작용 또는 스캔 및 수집을 위한 개인 링크로 제한 됩니다. 

현재 Azure 부서의 범위 방화벽은 부서의 범위 계정의 공용 끝점에 대 한 액세스 제어를 제공 합니다. 방화벽을 사용 하 여 모든 액세스를 허용 하거나 개인 끝점을 사용 하는 경우 공용 끝점을 통해 모든 액세스를 차단할 수 있습니다. 

네트워크, 연결 및 보안 요구 사항에 따라 Azure 부서의 범위 계정을 설정 하 고 유지 관리 하 여 기본 서비스 또는 수집에 액세스할 수 있습니다. 이 모범 사례 가이드를 사용 하 여 네트워크 환경을 정의 하 고 준비 하 여 네트워크 또는 클라우드의 다양 한 위치에서 Azure 부서의 범위에 액세스 하 고 데이터 원본을 검색할 수 있도록 합니다. 

이 가이드에서는 다음 네트워크 옵션에 대해 설명 합니다. 

- [Azure 공용 끝점](#option-1-use-public-endpoints)을 사용 합니다. 
- [전용 끝점](#option-2-use-private-endpoints)을 사용 합니다. 
- [개인 끝점을 사용 하 고 동일한 Azure 부서의 범위 계정에 대 한 공용 액세스를 허용](#option-3-use-both-private-and-public-endpoints)합니다. 

이 가이드는 Azure 부서의 범위에 대 한 가장 일반적인 몇 가지 네트워크 아키텍처 시나리오를 설명 합니다. 이러한 시나리오를 제한 하지는 않지만 Azure 부서의 범위 계정에 대 한 네트워킹을 계획할 때 서비스의 [제한](#current-limitations) 사항을 염두에 두십시오. 

이 가이드의 대상 사용자는 다음을 포함 합니다.

- 데이터 아키텍처 팀 
- 네트워크 팀  
- 데이터 보안 팀 

## <a name="prerequisites"></a>사전 요구 사항

사용자 환경에 가장 적합 한 네트워크 옵션을 이해 하려면 먼저 다음 작업을 수행 하는 것이 좋습니다. 

- Azure 부서의 범위에서 데이터 원본을 등록 하 고 검색 하기 전에 네트워크 토폴로지와 보안 요구 사항을 검토 하세요. 자세한 내용은 [Azure 네트워크 토폴로지 정의](/azure/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology)를 참조 하세요. 

- [PaaS 서비스에 대 한 네트워크 연결 모델을](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services)정의 합니다. 

## <a name="option-1-use-public-endpoints"></a>옵션 1: 공용 끝점 사용 

기본적으로 인터넷을 통해 액세스할 수 있는 공용 끝점을 통해 Azure 부서의 범위 계정을 사용할 수 있습니다. 다음 요구 사항이 있는 경우 Azure 부서의 범위 계정에서 공용 네트워크를 허용 합니다. 

- Azure 부서의 범위 끝점을 검색 하거나 연결할 때 개인 연결이 필요 하지 않습니다. 
- 모든 데이터 원본은 SaaS 응용 프로그램 전용입니다. 
- 모든 데이터 원본에는 인터넷을 통해 액세스할 수 있는 공용 끝점이 있습니다. 
- 비즈니스 사용자는 인터넷을 통해 Azure 부서의 범위 계정 및 Azure 부서의 범위 Studio에 액세스 해야 합니다. 

### <a name="integration-runtime-options"></a>Integration runtime 옵션 

Azure 부서의 범위 계정 방화벽이 공용 액세스를 허용 하도록 설정 된 상태에서 데이터 원본을 검색 하려면 Azure integration runtime 및 [자체 호스팅 통합 런타임을](./manage-integration-runtimes.md)모두 사용할 수 있습니다. 이를 사용 하는 방법은 [데이터 원본의 지원 가능성](manage-data-sources.md)에 따라 달라 집니다.  

다음은 몇 가지 모범 사례입니다.

- azure integration runtime 또는 자체 호스팅 integration runtime을 사용 하 여 Azure SQL Database 또는 Azure Blob Storage 같은 azure 데이터 원본을 검색할 수 있습니다. 가능 하면 azure integration runtime을 사용 하 여 Azure 데이터 원본을 검색 하는 것이 좋습니다 .이를 통해 비용 및 관리 오버 헤드를 줄일 수 있습니다. 
  
- 여러 Azure 데이터 원본을 검색 하려면 공용 네트워크 및 Azure integration runtime을 사용 합니다. 다음 단계는 azure integration runtime을 사용 하 여 Azure에서 데이터 원본을 검색할 때 높은 수준의 통신 흐름을 보여 줍니다.

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="Azure 부서의 범위, Azure 런타임 및 데이터 원본 간의 연결 흐름을 보여 주는 스크린샷"lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. 수동 또는 자동 검색은 azure 통합 런타임을 통해 Azure 부서의 범위 데이터 맵에서 시작 됩니다. 
   
  2. Azure 통합 런타임은 데이터 원본에 연결 하 여 메타 데이터를 추출 합니다.

  3. 메타 데이터는 Azure 부서의 범위 관리 저장소에서 큐에 저장 되 고 Azure Blob Storage에 저장 됩니다. 

  4. 메타 데이터는 Azure 부서의 범위 데이터 맵으로 전송 됩니다. 

- 온-프레미스 및 VM 기반 데이터 원본을 스캔 하려면 항상 자체 호스팅 통합 런타임을 사용 해야 합니다. Azure integration runtime은 이러한 데이터 원본에 대해 지원 되지 않습니다. 다음 단계는 자체 호스팅 통합 런타임을 사용 하 여 데이터 원본을 검색할 때 높은 수준의 통신 흐름을 보여 줍니다.

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="Azure 부서의 범위, 자체 호스팅 런타임 및 데이터 원본 간의 연결 흐름을 보여 주는 스크린샷"lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. 수동 또는 자동 검사가 트리거됩니다. Azure 부서의 범위는 Azure Key Vault에 연결 하 여 데이터 원본에 액세스 하기 위한 자격 증명을 검색 합니다.
   
  2. 검색은 자체 호스팅 통합 런타임을 통해 Azure 부서의 범위 데이터 맵에서 시작 됩니다. 
   
  3. VM의 자체 호스팅 integration runtime 서비스는 데이터 원본에 연결 하 여 메타 데이터를 추출 합니다.

  4. 메타 데이터는 자체 호스팅 통합 런타임에 대 한 VM 메모리에서 처리 됩니다. 메타 데이터는 Azure 부서의 범위 관리 저장소에서 큐에 대기 된 다음 Azure Blob Storage에 저장 됩니다. 

  5. 메타 데이터는 Azure 부서의 범위 데이터 맵으로 전송 됩니다. 

### <a name="authentication-options"></a>인증 옵션  

Azure 부서의 범위에서 데이터 원본을 검색 하는 경우 자격 증명을 제공 해야 합니다. 그런 다음 azure 부서의 범위는 대상 데이터 원본에서 Azure integration runtime을 사용 하 여 자산의 메타 데이터를 읽을 수 있습니다. 공용 네트워크를 사용 하는 경우 인증 옵션 및 요구 사항은 다음 요소에 따라 다릅니다. 

- **데이터 원본 유형** 입니다. 예를 들어 데이터 원본이 Azure SQL Database 되는 경우 각 데이터베이스에 대 한 db_datareader 액세스와 SQL 인증을 사용 해야 합니다. 사용자 관리 id 또는 Azure 부서의 범위 관리 id 일 수 있습니다. 또는 db_datareader SQL Database에 추가 된 Azure Active Directory의 서비스 사용자 일 수 있습니다. 

  데이터 원본이 Azure Blob Storage 되는 경우 azure Storage 계정에 Blob Storage 데이터 판독기 역할로 추가 된 Azure Active Directory의 azure 부서의 범위 관리 id 또는 서비스 주체를 사용할 수 있습니다. 또는 단순히 저장소 계정의 키를 사용 합니다.  

- **인증 유형** 입니다. 가능 하면 azure 부서의 범위 관리 id를 사용 하 여 Azure 데이터 원본을 검색 하는 것이 좋습니다 .이를 통해 관리 오버 헤드를 줄일 수 있습니다. 다른 인증 유형의 경우 [Azure 부서의 범위 내에서 원본 인증에 대 한 자격 증명을 설정](manage-credentials.md)해야 합니다. 

  1. Azure 키 자격 증명 모음 내에서 비밀을 생성 합니다. 
  1. Azure 부서의 범위 내에 키 자격 증명 모음을 등록 합니다.  
  1. Azure 부서의 범위 내에서 키 자격 증명 모음에 저장 된 암호를 사용 하 여 새 자격 증명을 만듭니다. 

- **검색에 사용 되는 런타임 형식** 입니다. 현재는 자체 호스팅 통합 런타임에서 Azure 부서의 범위 관리 id를 사용할 수 없습니다. 

### <a name="additional-considerations"></a>기타 고려 사항  

- 공용 끝점을 사용 하 여 데이터 원본을 검색 하도록 선택 하는 경우 온-프레미스 또는 VM 기반 데이터 원본에서 Azure 끝점에 대 한 아웃 바운드 연결을 사용 해야 합니다. 

- 자체 호스팅 통합 런타임 Vm은 [Azure 끝점에 대 한 아웃 바운드 연결을](manage-integration-runtimes.md#networking-requirements)포함 해야 합니다. 

- Azure 데이터 원본은 공용 액세스를 허용 해야 합니다. 데이터 원본에서 서비스 끝점을 사용 하는 경우 _신뢰할 수 있는 서비스 목록에서 azure 서비스를 허용_ 하 여 azure 데이터 원본에 액세스 해야 합니다. 서비스 끝점은 Azure에 대 한 최적의 경로를 통해 가상 네트워크에서 트래픽을 라우팅합니다. 

## <a name="option-2-use-private-endpoints"></a>옵션 2: 전용 끝점 사용 

Azure 부서의 범위 계정에 대해 [azure 개인 끝점](../private-link/private-endpoint-overview.md) 을 사용할 수 있습니다. 이 옵션은 다음 중 하나를 수행 해야 하는 경우에 유용 합니다.

- 개인 연결을 통해 azure 가상 네트워크 및 온-프레미스 데이터 원본 내의 Azure IaaS (infrastructure as a service) 및 PaaS 데이터 원본을 스캔 합니다.
- 가상 네트워크의 사용자가 [Azure 개인 링크](../private-link/private-link-overview.md)를 통해 azure 부서의 범위에 안전 하 게 액세스할 수 있도록 허용 합니다. 

다른 PaaS 솔루션과 마찬가지로 Azure 부서의 범위는 가상 네트워크에 직접 배포 하는 것을 지원 하지 않습니다. 따라서 네트워크 보안 그룹, 경로 테이블 또는 Azure 방화벽과 같은 다른 네트워크 종속 어플라이언스와 같은 제공 리소스에 특정 네트워킹 기능을 사용할 수 없습니다. 대신 가상 네트워크에서 사용할 수 있는 개인 끝점을 사용할 수 있습니다. 그런 다음 공용 인터넷 액세스를 사용 하지 않도록 설정 하 여 Azure 부서의 범위에 안전 하 게 연결할 수 있습니다. 

다음 요구 사항이 있는 경우 Azure 부서의 범위 계정에 대해 개인 끝점을 사용 해야 합니다. 

- Azure 부서의 범위 계정 및 데이터 원본에 대 한 종단 간 네트워크 격리가 필요 합니다. 

- Azure 부서의 범위 계정에 대 한 [공용 액세스를 차단](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access) 해야 합니다. 

- PaaS 데이터 원본은 개인 끝점을 사용 하 여 배포 되며, 공용 끝점을 통해 모든 액세스를 차단 했습니다. 

- 온-프레미스 또는 IaaS 데이터 원본은 공용 끝점에 도달할 수 없습니다. 

### <a name="design-considerations"></a>디자인 고려 사항  

- 개인적으로 안전 하 게 Azure 부서의 범위 계정에 연결 하려면 계정 및 포털 개인 끝점을 배포 해야 합니다. 예를 들어 API를 통해 Azure 부서의 범위에 연결 하거나 Azure 부서의 범위 Studio를 사용 하려는 경우이 배포가 필요 합니다.

- 개인 끝점을 사용 하 여 Azure 부서의 범위 Studio에 연결 해야 하는 경우 계정 및 포털 개인 끝점을 둘 다 배포 해야 합니다. 

- 개인 연결을 통해 데이터 원본을 검색 하려면 하나 이상의 계정 및 Azure 부서의 범위에 대 한 수집 개인 끝점을 하나 이상 구성 해야 합니다. Azure 부서의 범위 관리 id 이외의 인증 방법을 통해 자체 호스팅 통합 런타임을 사용 하 여 검사를 구성 해야 합니다. 

- 검색을 설정 하기 전에 수집 [개인 끝점을 통해 데이터 원본 검색을 위한 지원 매트릭스를](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint) 검토 합니다.

- [DNS 요구 사항을](catalog-private-link-name-resolution.md#deployment-options)검토 합니다. 네트워크에서 사용자 지정 DNS 서버를 사용 하는 경우 클라이언트는 개인 끝점의 IP 주소에 대 한 Azure 부서의 범위 계정 끝점의 FQDN (정규화 된 도메인 이름)을 확인할 수 있어야 합니다. 

### <a name="integration-runtime-options"></a>Integration runtime 옵션 

- 데이터 원본이 azure에 있는 경우 azure 부서의 범위 수집 개인 끝점을 배포 하는 동일한 가상 네트워크 내에 배포 된 Windows 가상 컴퓨터에서 자체 호스팅 통합 런타임을 설정 하 고 사용 해야 합니다. Azure integration runtime은 수집 전용 끝점에서 작동 하지 않습니다. 

- 온-프레미스 데이터 원본을 검색 하기 위해 온-프레미스 Windows 컴퓨터 또는 Azure virtual network 내의 VM에 자체 호스팅 통합 런타임을 설치할 수도 있습니다. 

- Azure 부서의 범위에서 개인 끝점을 사용 하는 경우 azure 부서의 범위 개인 끝점을 배포 하는 Azure 가상 네트워크에서 자체 호스팅 통합 VM에 대 한 데이터 원본에서의 네트워크 연결을 허용 해야 합니다.  

- 자체 호스팅 integration runtime의 자동 업그레이드를 허용 하는 것이 좋습니다. Azure 가상 네트워크 또는 회사 방화벽에서 필요한 아웃 바운드 규칙을 열어 자동 업그레이드를 허용 해야 합니다. 자세한 내용은 [자체 호스팅 통합 런타임 네트워킹 요구 사항](manage-integration-runtimes.md#networking-requirements)을 참조 하세요.

### <a name="authentication-options"></a>인증 옵션  

- Azure 부서의 범위 관리 id를 사용 하 여 수집 전용 끝점을 통해 데이터 소스를 검색할 수 없습니다. 데이터 원본 유형에 따라 서비스 주체, 계정 키 또는 SQL 인증을 사용 합니다.

- 자격 증명이 Azure key vault에 저장 되 고 Azure 부서의 범위 내에 등록 되었는지 확인 합니다.

- Azure 키 자격 증명 모음에서 만든 각 암호에 따라 Azure 부서의 범위에서 자격 증명을 만들어야 합니다. Azure의 Key Vault 리소스에서 Azure 부서의 범위의 비밀에 대 한 _get_ 및 _list_ 액세스를 최소한으로 할당 해야 합니다. 그렇지 않으면 자격 증명이 Azure 부서의 범위 계정에서 작동 하지 않습니다. 

### <a name="current-limitations"></a>현재 제한 사항 

- 수집을 위해 개인 끝점을 사용 하는 경우 수집 전용 끝점 및 자체 호스팅 통합 런타임을 통해 전체 구독 또는 리소스 그룹을 사용 하 여 여러 Azure 소스를 검색 하는 것은 지원 되지 않습니다. 대신 데이터 원본을 개별적으로 등록 하 고 검색할 수 있습니다. 

- Azure 부서의 범위 개인 끝점과 관련 된 제한 사항은 [알려진 제한](catalog-private-link-troubleshoot.md#known-limitations)을 참조 하세요.

- 개인 링크 서비스와 관련 된 제한 사항은 [Azure 개인 링크 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)을 참조 하세요. 

### <a name="private-endpoint-scenarios"></a>전용 끝점 시나리오 

#### <a name="single-virtual-network-single-region"></a>단일 가상 네트워크, 단일 지역  

이 시나리오에서 모든 Azure 데이터 원본, 자체 호스팅 통합 런타임 Vm 및 Azure 부서의 범위 개인 끝점은 Azure 구독의 동일한 가상 네트워크에 배포 됩니다.   

온-프레미스 데이터 원본이 있는 경우 azure 부서의 범위 개인 끝점이 배포 된 azure 가상 네트워크에 대 한 사이트 간 VPN 또는 Azure Express 경로 연결을 통해 연결이 제공 됩니다. 

이 아키텍처는 소규모 조직이 나 개발, 테스트 및 개념 증명 시나리오에 적합 합니다. 

:::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="단일 가상 네트워크 시나리오에서 개인 끝점을 사용 하 여 Azure 부서의 범위를 보여 주는 스크린샷"lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="single-region-multiple-virtual-networks"></a>단일 지역, 여러 가상 네트워크 

Azure에서 두 개 이상의 가상 네트워크를 연결 하려면 [가상 네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md)을 사용할 수 있습니다. 피어링된 가상 네트워크 간의 네트워크 트래픽은 비공개이며 Azure 백본 네트워크에 유지됩니다. 

많은 고객이 허브 및 스포크 네트워크 아키텍처를 사용 하 여 Azure에서 네트워크 인프라를 구축 합니다. 

- 네트워킹 공유 서비스 (예: 네트워크 가상 어플라이언스, Express 경로/VPN 게이트웨이 또는 DNS 서버)는 허브 가상 네트워크에 배포 됩니다. 
- 스포크 가상 네트워크는 가상 네트워크 피어 링을 통해 이러한 공유 서비스를 사용 합니다. 

허브 및 스포크 네트워크 아키텍처에서 조직의 데이터 거 버 넌 스 팀은 가상 네트워크 (허브)를 포함 하는 Azure 구독과 함께 제공 될 수 있습니다. 모든 데이터 서비스는 가상 네트워크 피어 링 또는 사이트 간 VPN 연결을 통해 허브 가상 네트워크에 연결 된 몇 가지 다른 구독에 있을 수 있습니다. 

허브 및 스포크 아키텍처에서는 Azure 부서의 범위 및 하나 이상의 자체 호스팅 통합 런타임 Vm을 허브 구독 및 가상 네트워크에 배포할 수 있습니다. 동일한 지역에 있는 여러 구독의 다른 가상 네트워크에서 데이터 원본을 등록 하 고 검색할 수 있습니다. 

자체 호스팅 integration runtime Vm은 수집 개인 끝점과 동일한 가상 네트워크에 있어야 하지만 별도의 서브넷에 있을 수 있습니다.

:::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="여러 가상 네트워크의 시나리오에서 개인 끝점을 사용 하 여 Azure 부서의 범위를 보여 주는 스크린샷"lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

필요에 따라 스포크 가상 네트워크에서 자체 호스팅 통합 런타임을 추가로 배포할 수 있습니다. 이 경우 추가 계정을 배포 하 고 스포크 가상 네트워크에서 개인 끝점을 수집 해야 합니다. 

#### <a name="multiple-regions-multiple-virtual-networks"></a>여러 지역, 여러 가상 네트워크

데이터 원본이 하나 이상의 Azure 구독에 있는 여러 Azure 지역에 분산 되어 있는 경우이 시나리오를 사용할 수 있습니다.

성능 및 비용 최적화를 위해 데이터 원본이 위치한 각 지역에 하나 이상의 자체 호스팅 통합 런타임 Vm을 배포 하는 것이 좋습니다. 이 경우 자체 호스팅 통합 런타임 Vm이 있는 지역 및 가상 네트워크에서 Azure 부서의 범위 계정에 대 한 추가 계정 및 수집 개인 끝점을 배포 해야 합니다.  

다른 지역에서 모든 Azure Data Lake Storage (Gen2) 리소스를 등록 하 고 검색 해야 하는 경우에는 데이터 원본이 있는 지역에 자체 호스팅 integration runtime VM이 있어야 합니다. 

:::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="여러 가상 네트워크 및 여러 지역의 시나리오에서 개인 끝점을 사용 하 여 Azure 부서의 범위를 보여 주는 스크린샷"lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3-use-both-private-and-public-endpoints"></a>옵션 3: 개인 및 공용 끝점 모두 사용

데이터 원본 하위 집합이 개인 끝점을 사용 하는 옵션을 선택 하 고 동시에 다음 중 하나를 검색 해야 하는 경우를 선택할 수 있습니다.

- [서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) 으로 구성 된 기타 데이터 원본
- 인터넷을 통해 액세스할 수 있는 공용 끝점을 포함 하는 데이터 원본

공용 끝점 또는 서비스 끝점을 사용 하 여 수집 개인 끝점 및 일부 데이터 소스를 사용 하 여 일부 데이터 소스를 검색 해야 하는 경우 다음을 수행할 수 있습니다.

1. Azure 부서의 범위 계정에 대 한 개인 끝점을 사용 합니다.
1. Azure 부서의 범위 계정에서 **허용** 하도록 **공용 네트워크 액세스** 를 설정 합니다.

### <a name="integration-runtime-options"></a>Integration runtime 옵션 

- 개인 끝점을 사용 하 여 구성 된 azure 데이터 원본을 검색 하려면 azure 부서의 범위 수집 개인 끝점을 배포 하는 동일한 가상 네트워크 내에 배포 된 Windows 가상 컴퓨터에서 자체 호스팅 통합 런타임을 설정 하 고 사용 해야 합니다. 

  Azure 부서의 범위에서 개인 끝점을 사용 하는 경우 azure 부서의 범위 개인 끝점이 배포 된 Azure 가상 네트워크에서 자체 호스팅 통합 VM에 대 한 데이터 원본에서의 네트워크 연결을 허용 해야 합니다.  

- 공용 끝점을 허용 하도록 구성 된 Azure 데이터 원본을 검색 하기 위해 Azure integration runtime을 사용할 수 있습니다. 

- 온-프레미스 데이터 원본을 검색 하기 위해 온-프레미스 Windows 컴퓨터 또는 Azure virtual network 내의 VM에 자체 호스팅 통합 런타임을 설치할 수도 있습니다. 

- 자체 호스팅 통합 런타임에 대해 자동 업그레이드를 허용 하는 것이 좋습니다. Azure 가상 네트워크 또는 회사 방화벽에서 필요한 아웃 바운드 규칙을 열어 자동 업그레이드를 허용 해야 합니다. 자세한 내용은 [자체 호스팅 통합 런타임 네트워킹 요구 사항](manage-integration-runtimes.md#networking-requirements)을 참조 하세요.

### <a name="authentication-options"></a>인증 옵션  

- 공용 끝점을 허용 하도록 구성 된 Azure 데이터 원본을 검색 하려면 데이터 원본 유형에 따라 모든 인증 옵션을 사용할 수 있습니다.
  
- 수집 개인 끝점을 사용 하 여 개인 끝점으로 구성 된 Azure 데이터 소스를 검색 하는 경우:

  - Azure 부서의 범위 관리 id를 사용할 수 없습니다. 대신 데이터 원본 유형에 따라 서비스 주체, 계정 키 또는 SQL 인증을 사용 합니다. 
  
  - 자격 증명이 Azure key vault에 저장 되 고 Azure 부서의 범위 내에 등록 되었는지 확인 합니다.

  - Azure Key Vault에서 만든 각 암호에 따라 Azure 부서의 범위에서 자격 증명을 만들어야 합니다. Azure의 Key Vault 리소스에서 Azure 부서의 범위의 비밀에 대 한 _get_ 및 _list_ 액세스를 할당 합니다. 그렇지 않으면 자격 증명이 Azure 부서의 범위 계정에서 작동 하지 않습니다. 

## <a name="next-steps"></a>다음 단계
-  [Azure 부서의 범위에 대 한 보안 액세스를 위한 개인 끝점 사용](./catalog-private-link.md)
