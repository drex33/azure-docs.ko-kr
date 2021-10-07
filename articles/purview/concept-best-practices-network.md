---
title: Purview 네트워크 아키텍처 및 모범 사례
description: 이 문서에서는 Azure Purview 네트워크 아키텍처 옵션의 예제를 제공하고 모범 사례를 설명합니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: b49709a5fb31e597b6a6f85ac0648018ea35a744
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663030"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Azure Purview 네트워크 아키텍처 및 모범 사례

Azure Purview는 Platform as a Service 데이터 거버넌스 솔루션입니다. 

Azure Purview 계정에는 Purview에 연결하기 위해 인터넷을 통해 액세스할 수 있는 퍼블릭 엔드포인트가 있습니다. 그러나 모든 엔드포인트는 세분화된 액세스 제어가 있는 AAD 로그인 및 RBAC를 사용하여 보호됩니다.
추가 보안 계층의 경우 Azure의 가상 네트워크에서 Purview 계정 및 관리되는 리소스에 개인 IP 주소를 할당하는 Azure Purview 계정에 대한 프라이빗 엔드포인트를 만들 수도 있습니다. 이렇게 하면 사용자가 API, Purview Studio 또는 검색 및 검색을 위해 상호 작용하는 경우 가상 네트워크와 Purview 계정 간의 모든 트래픽이 프라이빗 링크로 제한됩니다. 현재 Azure Purview 방화벽은 purview 계정의 퍼블릭 엔드포인트에 대한 액세스 제어를 제공합니다. 방화벽을 사용하여 프라이빗 엔드포인트를 사용할 때 퍼블릭 엔드포인트를 통한 모든 액세스를 허용하거나 차단할 수 있습니다. 

네트워크 및 보안 요구 사항에 따라 다음 네트워크 옵션에 따라 Purview 기본 서비스 또는 집계에 액세스하도록 Azure Purview 계정을 설정하고 유지 관리합니다. 

- [Azure 공용 엔드포인트 를 사용합니다.](#option-1---use-public-endpoints) 
- [프라이빗 엔드포인트 를](#option-2---use-private-endpoints)사용합니다. 
- [프라이빗 엔드포인트를 사용하고 동일한 Purview 계정 에서 공용 액세스를 허용합니다.](#option-3---use-both-private-endpoint-and-public-endpoints) 

사용자 환경에 가장 적합한 옵션을 이해하려면 먼저 다음 작업을 수행하는 것이 좋습니다. 

- Azure Purview에서 데이터 원본을 등록하고 검사하기 전에 네트워크 토폴로지 및 보안 요구 사항을 검토합니다. 자세한 내용은 [Azure 네트워크 토폴로지 정의를 참조하세요.](/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology) 

- [PaaS 서비스에 대한 네트워크 연결 모델을 정의합니다.](/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services) 

## <a name="intended-audience"></a>대상 그룹  

- 데이터 아키텍처 팀 
- 네트워크 팀  
- 데이터 보안 팀 

## <a name="why-do-you-need-to-consider-defining-a-networking-design-part-of-azure-purview-architecture"></a>Azure Purview 아키텍처의 일부인 네트워킹 디자인을 정의해야 하는 이유는 무엇인가요? 

이 모범 사례 가이드를 사용하여 네트워크, 연결 및 보안 요구 사항에 따라 네트워크 또는 클라우드의 다양한 위치에서 Purview에 액세스하고 데이터 원본을 검사할 수 있도록 Azure Purview에 대한 네트워크 환경을 정의하고 준비합니다.

이 가이드에서는 Azure Purview에 대한 가장 일반적인 몇 가지 네트워크 아키텍처 시나리오를 설명했습니다. 그러나 옵션은 이 문서로 제한되지 않습니다. Purview 계정에 대한 네트워킹을 계획할 때 Purview 제한 사항 검토 

## <a name="option-1---use-public-endpoints"></a>옵션 1 - 퍼블릭 엔드포인트 사용 

기본적으로 인터넷을 통해 액세스할 수 있는 퍼블릭 엔드포인트를 통해 Azure Purview 계정을 사용할 수 있습니다. 다음 요구 사항이 있는 경우 Purview 계정에서 공용 네트워크를 허용합니다. 

- Azure Purview 엔드포인트를 검색하거나 연결할 때는 프라이빗 연결이 필요하지 않습니다. 
- 모든 데이터 원본은 SaaS 애플리케이션 전용입니다. 
- 모든 데이터 원본에는 인터넷을 통해 액세스할 수 있는 공용 엔드포인트가 있습니다. 
- 비즈니스 사용자는 인터넷을 통해 Purview 계정 및 Purview Studio에 액세스해야 합니다. 

### <a name="integration-runtime-options"></a>Integration Runtime 옵션 

Purview 계정 방화벽이 공용 액세스 허용으로 설정된 동안 데이터 원본을 검사하려면 [데이터 원본 지원 가능성에](manage-data-sources.md)따라 Azure 통합 런타임 및 자체 호스팅 통합 [런타임](./manage-integration-runtimes.md)을 모두 사용할 수 있습니다.  

- Azure 통합 런타임 또는 자체 호스팅 통합 런타임을 사용하여 Azure SQL Database 또는 Azure Blob Storage 같은 Azure 데이터 원본을 검사할 수 있습니다.  

- 비용 및 관리 오버헤드를 줄이기 위해 가능하면 Azure 통합 런타임을 사용하여 Azure 데이터 원본을 검사하는 것이 좋습니다. 
  
- 여러 Azure 데이터 원본을 검색하려면 공용 네트워크 및 Azure 통합 런타임을 사용합니다. 다음 단계에서는 Azure 통합 런타임을 사용하여 Azure에서 데이터 원본을 검사할 때 매우 높은 수준의 통신 흐름을 보여 드리고 있습니다.

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="Azure Purview, 런타임 및 데이터 원본 간의 연결 흐름을 보여 주는 스크린샷."lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. 수동 또는 자동 검사는 Azure 통합 런타임을 사용하여 Purview 데이터 맵에서 시작됩니다. 
   
  2. Azure 통합 런타임은 데이터 원본에 연결하여 메타데이터를 추출합니다.

  3. 메타데이터는 Azure Purview의 관리형 스토리지에 큐에 저장되고 Azure Blob Storage에 저장됩니다. 

  4. 메타데이터는 Azure Purview 데이터 맵으로 전송됩니다. 

- 온-프레미스 및 VM 기반 데이터 원본을 검사하려면 항상 자체 호스팅 통합 런타임을 사용해야 합니다. 이러한 데이터 원본에는 Azure 통합 런타임이 지원되지 않습니다. 다음 단계에서는 자체 호스팅 통합 런타임을 사용하여 데이터 원본을 검사할 때 매우 높은 수준의 통신 흐름을 보여줍니다.

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="Azure Purview, 자체 호스팅 런타임 및 데이터 원본 간의 연결 흐름을 보여 주는 스크린샷."lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. 수동 또는 자동 검색이 트리거됩니다. Azure purview는 Azure Key Vault 연결하여 데이터 원본에 액세스하기 위한 자격 증명을 검색합니다.
   
  2. 검사는 자체 호스팅 통합 런타임을 사용하여 Purview 데이터 맵에서 시작됩니다. 
   
  3. 가상 머신의 자체 호스팅 통합 런타임 서비스는 데이터 원본에 연결하여 메타데이터를 추출합니다.

  4. 메타데이터는 자체 호스팅 통합 런타임 VM 메모리에서 처리됩니다. 메타데이터는 Azure Purview의 관리되는 스토리지에 큐에 대기한 다음, Azure Blob Storage에 저장됩니다. 

  5. 메타데이터는 Azure Purview 데이터 맵으로 전송됩니다. 

### <a name="authentication-options"></a>인증 옵션  

Azure Purview에서 데이터 원본을 검색할 때 Azure Purview가 대상 데이터 원본의 Azure 통합 런타임을 사용하여 자산의 메타데이터를 읽을 수 있도록 자격 증명을 제공해야 합니다. 공용 네트워크를 사용하는 경우 인증 옵션 및 요구 사항은 다음 요인에 따라 달라집니다. 

- 데이터 원본 유형입니다. 예를 들어 데이터 원본이 Azure SQL Database 경우 각 데이터베이스에 대한 액세스 권한이 db_datareader SQL 인증을 사용해야 합니다. db_datareader Azure SQL Database 추가된 Azure Active Directory 사용자 또는 Azure Purview 관리 ID 또는 서비스 주체일 수 있습니다. 데이터 원본이 Azure Blob Storage 경우 Azure Storage 계정에서 Storage Blob 데이터 판독기 역할로 추가된 Azure Active Directory Azure Purview 관리 ID 또는 서비스 주체를 사용하거나 Storage 계정의 키를 사용할 수 있습니다.  

- 관리 오버헤드를 줄이기 위해 가능하면 Azure Purview 관리 ID를 사용하여 Azure 데이터 원본을 검사하는 것이 좋습니다. 다른 인증 유형의 경우 [Azure Purview 내에서 원본 인증에 대한 자격 증명을 설정해야](manage-credentials.md)합니다. 

  - Azure Key Vault 내에 비밀을 생성합니다. 
  - Azure Purview 내에 키 자격 증명 모음을 등록합니다.  
  - Azure Purview 내에서 Azure Key Vault 저장된 비밀을 사용하여 새 자격 증명을 만듭니다. 

- 검색에 사용되는 런타임의 형식입니다. 현재 자체 호스팅 통합 런타임에는 Azure Purview 관리 ID를 사용할 수 없습니다. 

### <a name="additional-considerations"></a>기타 고려 사항  

- 퍼블릭 엔드포인트를 사용하여 데이터 원본을 검사하도록 선택하는 경우 온-프레미스 또는 VM 기반 데이터 원본은 Azure 엔드포인트에 대한 아웃바운드 연결이 있어야 합니다. 

- 자체 호스팅 통합 런타임 VM은 [Azure 엔드포인트 에 대한 아웃바운드 연결이](manage-integration-runtimes.md#networking-requirements)있어야 합니다. 

- Azure 데이터 원본은 공용 액세스를 허용해야 하지만 데이터 원본에서 서비스 엔드포인트를 사용하도록 설정한 경우 신뢰할 수 있는 _서비스 목록의 Azure 서비스가_ Azure 데이터 원본에 액세스할 수 있도록 허용해야 합니다. 서비스 엔드포인트는 최적의 경로를 통해 VNet에서 Azure로 트래픽을 라우팅합니다. 

## <a name="option-2---use-private-endpoints"></a>옵션 2 - 프라이빗 엔드포인트 사용 

프라이빗 연결을 통해 Azure 가상 네트워크 및 온-프레미스 데이터 원본 내에서 Azure IaaS 및 PaaS 데이터 원본을 검사하거나 가상 네트워크의 사용자가 [Private Link](../private-link/private-link-overview.md)통해 Azure Purview에 안전하게 액세스하도록 허용해야 하는 경우 Azure Purview 계정에 Azure [프라이빗 엔드포인트를](../private-link/private-endpoint-overview.md) 사용할 수 있습니다. 

다른 Platform as a Service 솔루션과 마찬가지로 Azure Purview는 가상 네트워크에 직접 배포하는 것을 지원하지 않습니다. 이 때문에 네트워크 보안 그룹, 경로 테이블 또는 기타 네트워크 종속 어플라이언스(예: Azure Firewall)와 같은 제품의 리소스와 함께 특정 네트워킹 기능을 활용할 수 없습니다. 대신 가상 네트워크에서 사용하도록 설정할 수 있는 프라이빗 엔드포인트를 사용할 수 있으며 퍼블릭 인터넷 액세스를 사용하지 않도록 설정하여 Purview에 안전하게 연결할 수 있습니다. 

다음 요구 사항을 충족해야 하는 경우 Azure Purview 계정에 프라이빗 엔드포인트를 사용해야 합니다. 

- Purview 계정 및 데이터 원본에 대한 엔드 투 엔드 네트워크 격리가 있어야 합니다. 

- Purview 계정에 [대한 공용 액세스를 차단해야](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access) 합니다. 

- PaaS 데이터 원본은 프라이빗 엔드포인트를 통해 배포되며 퍼블릭 엔드포인트를 통한 모든 액세스를 차단했습니다. 

- 온-프레미스 또는 IaaS 데이터 원본은 퍼블릭 엔드포인트에 연결할 수 없습니다. 

### <a name="design-considerations"></a>디자인 고려 사항  

- Purview 계정에 비공개 및 안전하게 연결하려면 계정 및 포털 프라이빗 엔드포인트를 배포해야 합니다. (예를 들어 API를 통해 Purview에 연결하거나 Azure Purview Studio를 시작하려는 경우). 

- 프라이빗 엔드포인트를 사용하여 Purview Studio에 연결해야 하는 경우 계정 및 포털 프라이빗 엔드포인트를 모두 배포해야 합니다. 

- 프라이빗 연결을 통해 데이터 원본을 검색하려면 Purview에 대해 하나 이상의 계정과 하나의 수집 프라이빗 엔드포인트를 구성해야 합니다. Azure Purview 관리 ID 이외의 인증 방법을 사용하여 자체 호스팅 통합 런타임을 사용하여 검사를 구성해야 합니다. 

- 검사를 설정하기 전에 [수집 프라이빗 엔드포인트를 통해 데이터 원본을 검사하기 위한 지원 매트릭스를](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint) 검토합니다.

- [DNS 요구 사항을 검토합니다.](catalog-private-link-name-resolution.md#deployment-options) 네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 Purview 계정 엔드포인트에 대한 FQDN을 프라이빗 엔드포인트 IP 주소로 확인할 수 있어야 합니다. 

### <a name="integration-runtime-options"></a>Integration Runtime 옵션 

- 데이터 원본이 Azure에 있는 경우 Azure Purview 수집 프라이빗 엔드포인트가 배포된 동일한 가상 네트워크 내에 배포된 Windows 가상 머신에서 자체 호스팅 통합 런타임을 설치하고 사용해야 합니다. Azure 통합 런타임은 프라이빗 엔드포인트를 통해서도 작동하지 않습니다. 

- 온-프레미스 데이터 원본을 검사하려면 온-프레미스 Windows 머신 또는 Azure 가상 네트워크 내의 VM에 자체 호스팅 통합 런타임을 설치할 수도 있습니다. 

- Azure Purview에서 프라이빗 엔드포인트를 사용하는 경우 Purview 프라이빗 엔드포인트가 배포된 Azure 가상 네트워크에서 데이터 원본에서 자체 호스팅 통합 VM으로의 네트워크 연결을 허용해야 합니다.  

- 자체 호스팅 통합 런타임 자동 업그레이드를 허용하는 것이 좋습니다. 자동 업그레이드를 허용하려면 Azure 가상 네트워크 또는 회사 방화벽에서 필요한 아웃바운드 규칙을 열어야 합니다. 자세한 내용은 [자체 호스팅 통합 런타임 네트워킹 요구 사항을 참조하세요.](manage-integration-runtimes.md#networking-requirements)

### <a name="authentication-options"></a>인증 옵션  

- Azure Purview MSI(관리 ID)를 사용하여 수집 프라이빗 엔드포인트를 통해 데이터 원본을 검사할 수 없습니다. 데이터 원본 유형에 따라 서비스 주체, 계정 키 또는 SQL 인증을 사용합니다.

- 자격 증명이 Azure Key Vault에 저장되고 Azure Purview 내에 등록되어 있는지 확인합니다.

- Azure Key Vault 만든 각 비밀을 기반으로 Azure Purview에서 자격 증명을 만들어야 합니다. Azure의 Key Vault 리소스에서 Azure Purview에 대한 _비밀에_ 대한 최소 get 및 _list_ 액세스를 할당해야 합니다. 그렇지 않으면 Azure Purview 계정에서 자격 증명이 작동하지 않습니다. 

### <a name="current-limitations"></a>현재 제한 사항 

- 수집 프라이빗 엔드포인트 및 자체 호스팅 통합 런타임을 사용하는 전체 구독 또는 리소스 그룹을 사용하여 Azure 여러 원본을 검색하는 것은 수집을 위해 프라이빗 엔드포인트를 사용하는 경우 지원되지 않습니다. 대신 데이터 원본을 개별적으로 등록하고 검사할 수 있습니다. 

- Azure Purview 프라이빗 엔드포인트와 관련된 제한은 [알려진 제한 사항](catalog-private-link-troubleshoot.md#known-limitations) 

- Private Link 서비스와 관련된 제한 사항은 [Azure Private Link 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)을 참조하세요. 

### <a name="private-endpoint-scenarios"></a>프라이빗 엔드포인트 시나리오 

#### <a name="scenario-1-single-vnet--single-region"></a>시나리오 1: 단일 VNet - 단일 지역  

이 시나리오에서는 모든 Azure 데이터 원본, 자체 호스팅 통합 런타임 VM 및 Purview 프라이빗 엔드포인트가 Azure 구독의 동일한 VNet에 배포됩니다.   

온-프레미스 데이터 원본이 있는 경우 사이트-사이트 VPN 또는 Azure Purview 프라이빗 엔드포인트가 배포된 Azure 가상 네트워크에 대한 ExpressRoute 연결을 통해 연결이 제공됩니다. 

이 아키텍처는 주로 소규모 조직 또는 개발, 테스트 및 개념 증명 시나리오에 적합합니다. 

  :::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="단일 vnet 시나리오에서 프라이빗 엔드포인트가 있는 Azure Purview를 보여 주는 스크린샷."lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="scenario-2-single-region--multiple-vnets"></a>시나리오 2: 단일 지역 – 여러 VNet 

Azure에서 둘 이상의 가상 네트워크를 함께 연결하려면 [가상 네트워크 피어링을](../virtual-network/virtual-network-peering-overview.md)사용할 수 있습니다. 피어링된 가상 네트워크 간의 네트워크 트래픽은 비공개이며 Azure 백본 네트워크에 유지됩니다. 

많은 고객이 허브 및 스포크 네트워크 아키텍처를 사용하여 Azure에서 네트워크 인프라를 구축합니다. 여기서는 다음을 수행합니다. 

- 네트워킹 공유 서비스(예: 네트워크 가상 어플라이언스, ExpressRoute/VPN 게이트웨이 또는 DNS 서버)는 허브 VNet(가상 네트워크)에 배포됩니다. 
- 스포크 VNet은 VNet 피어링을 통해 이러한 공유 서비스를 소비합니다. 

허브 및 스포크 네트워크 아키텍처에서 조직의 데이터 거버넌스 팀은 가상 네트워크(허브)를 포함하는 Azure 구독을 제공할 수 있으며, 모든 데이터 서비스는 VNet 피어링 또는 사이트 간 VPN 연결을 통해 허브 가상 네트워크에 연결된 몇 가지 다른 구독에 있을 수 있습니다. 이 아키텍처에서는 허브 구독 및 가상 네트워크에 Azure Purview 및 하나 이상의 자체 호스팅 통합 런타임 VM을 배포하고 동일한 지역의 여러 구독에서 다른 VNet의 데이터 원본을 등록하고 검사할 수 있습니다. 

자체 호스팅 통합 런타임 VM은 프라이빗 엔드포인트를 포함하는 것과 동일한 VNet에 있어야 하지만 별도의 서브넷에 있을 수 있습니다.

  :::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="다중 vnet 시나리오에서 프라이빗 엔드포인트가 있는 Azure Purview를 보여 주는 스크린샷."lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

필요에 따라 스포크 가상 네트워크에 추가 자체 호스팅 통합 런타임을 배포할 수 있습니다. 이 경우 스포크 가상 네트워크에 추가 계정 및 ingestion 프라이빗 엔드포인트를 배포해야 합니다. 

#### <a name="scenario-3-multiple-regions--multiple-vnets"></a>시나리오 3: 여러 지역 - 여러 VNet

데이터 원본이 하나 이상의 Azure 구독에서 여러 Azure 지역에 분산된 경우 이 시나리오를 사용할 수 있습니다.

성능 및 비용 최적화를 위해 데이터 원본이 있는 각 지역에 하나 이상의 자체 호스팅 통합 런타임 VM을 배포하는 것이 좋습니다. 이 경우 자체 호스팅 통합 런타임 VM이 있는 지역 및 VNet에서 Azure Purview 계정에 대한 추가 계정 및 프라이빗 엔드포인트를 배포해야 합니다.  

다른 지역의 Azure Data Lake Storage(Gen 2) 리소스를 등록하고 검사해야 하는 경우 데이터 원본이 있는 지역에 로컬 자체 호스팅 통합 런타임 VM이 있어야 합니다. 

  :::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="다중 vnet 및 다중 지역 시나리오에서 프라이빗 엔드포인트가 있는 Azure Purview를 보여 주는 스크린샷."lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3---use-both-private-endpoint-and-public-endpoints"></a>옵션 3 - 프라이빗 엔드포인트와 퍼블릭 엔드포인트 모두 사용

이 옵션은 프라이빗 엔드포인트를 사용하는 데이터 원본의 하위 집합이 있는 경우와 동시에 서비스를 통해 액세스할 수 있는 퍼블릭 엔드포인트가 있는 데이터 원본 또는 [서비스 엔드포인트로](../virtual-network/virtual-network-service-endpoints-overview.md) 구성된 다른 데이터 원본을 검색해야 하는 경우에 필요합니다.

다음 요구 사항을 충족해야 하는 경우 Azure Purview 계정에 프라이빗 엔드포인트를 사용하고 Purview 계정에서 _허용하도록_ **공용 네트워크 액세스를** 설정할 수 있습니다.
  
- 수집 프라이빗 엔드포인트를 사용하여 일부 데이터 원본을 검사하고 퍼블릭 엔드포인트 또는 서비스 엔드포인트를 사용하는 일부 데이터 원본을 검사해야 합니다.

### <a name="integration-runtime-options"></a>Integration Runtime 옵션 

- 프라이빗 엔드포인트로 구성된 Azure 데이터 원본을 검색하려면 Azure Purview 수집 프라이빗 엔드포인트가 배포된 동일한 가상 네트워크 내에 배포된 Windows 가상 머신에서 자체 호스팅 통합 런타임을 설치하고 사용해야 합니다. Azure Purview에서 프라이빗 엔드포인트를 사용하는 경우 Purview 프라이빗 엔드포인트가 배포된 Azure 가상 네트워크에서 데이터 원본에서 자체 호스팅 통합 VM으로의 네트워크 연결을 허용해야 합니다.  

- 퍼블릭 엔드포인트를 허용하도록 구성된 Azure 데이터 원본을 검색하려면 Azure 통합 런타임을 사용할 수 있습니다. 

- 온-프레미스 데이터 원본을 검사하려면 온-프레미스 Windows 머신 또는 Azure 가상 네트워크 내의 VM에 자체 호스팅 통합 런타임을 설치할 수도 있습니다. 

- 자체 호스팅 통합 런타임 자동 업그레이드를 허용하는 것이 좋습니다. 자동 업그레이드를 허용하려면 Azure 가상 네트워크 또는 회사 방화벽에서 필요한 아웃바운드 규칙을 열어야 합니다. 자세한 내용은 [자체 호스팅 통합 런타임 네트워킹 요구 사항을 참조하세요.](manage-integration-runtimes.md#networking-requirements)

### <a name="authentication-options"></a>인증 옵션  

- 퍼블릭 엔드포인트를 허용하도록 구성된 Azure 데이터 원본을 검색하려면 데이터 원본 유형에 따라 모든 인증 옵션을 사용할 수 있습니다.
  
- 수집 프라이빗 엔드포인트를 사용하여 프라이빗 엔드포인트로 구성된 Azure 데이터 원본 검색:
  - Azure Purview MSI(관리 ID)를 대신 사용할 수 없습니다. 대신 데이터 원본 유형에 따라 서비스 주체, 계정 키 또는 SQL 인증을 사용합니다. 
  
  - 자격 증명이 Azure Key Vault에 저장되고 Azure Purview 내에 등록되어 있는지 확인합니다.

  - Azure Key Vault 만든 각 비밀을 기반으로 Azure Purview에서 자격 증명을 만들어야 합니다. Azure의 Key Vault 리소스에서 Azure Purview에 대한 _비밀에_ 대한 최소 get 및 _list_ 액세스를 할당해야 합니다. 그렇지 않으면 Azure Purview 계정에서 자격 증명이 작동하지 않습니다. 

## <a name="next-steps"></a>다음 단계
-  [Purview에 대한 보안 액세스를 위해 프라이빗 엔드포인트 사용](./catalog-private-link.md)
