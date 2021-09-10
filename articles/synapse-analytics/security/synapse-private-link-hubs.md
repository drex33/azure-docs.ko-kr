---
title: 프라이빗 링크를 사용하여 Synapse Studio에 연결
description: 이 문서에서는 프라이빗 링크를 사용하여 Azure Synapse Studio에 연결하는 방법을 설명
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 967d7462ebdbbeab9d0653914dc0640af453b7ee
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291892"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Azure Private Link Hubs를 사용하여 Azure Synapse Studio에 연결 

이 문서에서는 Azure Synapse Analytics Private Link Hubs 프라이빗 링크를 사용하여 Synapse Studio에 안전하게 연결하는 방법을 설명합니다. 

## <a name="azure-private-link-hubs"></a>Azure Private Link Hubs 
프라이빗 링크를 사용하여 Azure 가상 네트워크에서 Azure Synapse Studio에 안전하게 연결할 수 있습니다. Azure Synapse Analytics 프라이빗 링크 허브는 보안 네트워크와 Synapse Studio 웹 환경 간에 커넥터 역할을 하는 Azure 리소스입니다. 

프라이빗 링크를 사용하여 Synapse Studio에 연결하는 두 단계가 있습니다. 첫째, 프라이빗 링크 허브 리소스를 만들어야 합니다. 둘째, Azure 가상 네트워크에서 해당 프라이빗 링크 허브로의 프라이빗 엔드포인트를 만들어야 합니다. 그러면 프라이빗 엔드포인트를 사용하여 Synapse Studio와 안전하게 통신할 수 있습니다. 프라이빗 엔드포인트를 DNS 솔루션(온-프레미스 솔루션 또는 Azure Private DNS)과 통합해야 합니다. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Azure Private Links Hubs 및 Azure Synapse Studio
Azure Synapse Studio를 사용하여 내 모든 Azure Synapse Analytics 작업 영역에 비공개로 연결하기 위해 단일 Azure Synapse 프라이빗 링크 허브 리소스를 사용할 수 있습니다. 작업 영역은 Azure Synapse Private 링크 허브와 동일한 지역에 있을 필요는 없습니다. Azure Synapse Private 링크 허브 리소스는 다른 구독 또는 Azure AD 테넌트에 대한 Synapse 작업 영역 연결에도 사용할 수 있습니다.

Azure Portal에서 ‘Synapse 프라이빗 링크 허브’를 검색하고 서비스에서 **Azure Synapse Analytics(프라이빗 링크 허브)** 를 선택하여 프라이빗 링크 허브를 만들 수 있습니다. 자세한 내용은 [제한된 네트워크에서 작업 영역 리소스에 연결](./how-to-connect-to-workspace-from-restricted-network.md)하는 방법에 대한 가이드의 단계를 따르세요.

>[!NOTE]
>프라이빗 링크 허브는 프라이빗 링크를 통해 고정적인 콘텐츠 Synapse Studio를 안전하게 로드하기 위한 것입니다. 작업 영역(예, 프로비저닝/전용 SQL 풀 또는 Spark 풀) 내에서 연결하고 싶은 리소스에 **별도의 프라이빗 엔드포인트** 를 생성해야 합니다. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure Private Links Hubs 및 Azure Virtual Network
Synapse Studio에 대한 엔드투엔드 연결을 보호하려면 Azure 가상 네트워크를 Synapse 프라이빗 링크 허브 리소스에 연결해야 합니다. 이렇게 하려면 가상 네트워크에서 생성된 프라이빗 링크 허브로의 프라이빗 엔드포인트를 만들어야 합니다. 프라이빗 링크 허브를 위해 Azure Portal을 사용할 수 있으며 프라이빗 엔드포인트 섹션으로 이동합니다. “+ 프라이빗 엔드포인트”를 선택하여 내 프라이빗 링크 허브에 연결하는 새 프라이빗 엔드포인트를 만듭니다.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="프라이빗 엔드포인트 연결 페이지를 보여 주는 스크린샷.":::

“리소스” 탭에서 “Microsoft.Synapse/privateLinkHubs” 리소스 종류를 선택해야 합니다.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="‘리소스 종류’가 강조 표시된 ‘프라이빗 엔드포인트 생성’ 페이지를 보여 주는 스크린샷.":::

가상 네트워크와 프라이빗 DNS 영역을 통합할 때 Private DNS Zones를 위해 “구성” 탭에서 “privatelink.azuresynapse.net”을 선택합니다.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="프라이빗 링크 허브에 프라이빗 엔드포인트 생성":::

## <a name="next-steps"></a>다음 단계

[제한된 네트워크에서 작업 영역 리소스에 연결](./how-to-connect-to-workspace-from-restricted-network.md)

[관리형 작업 영역 Virtual Network](./synapse-workspace-managed-vnet.md)에 대해 자세히 알아보기

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보

[데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)

[프라이빗 엔드포인트를 사용하여 Synapse 작업 영역에 연결](./how-to-connect-to-workspace-with-private-links.md)

