---
title: 프라이빗 링크를 사용하여 Synapse 작업 영역에 연결
description: 이 문서에서는 프라이빗 링크를 사용하여 Azure Synapse 작업 영역에 연결하는 방법을 설명합니다.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: c1ab7e4f30fbd297bc5aad79a8d250ec55684ddb
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215325"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links"></a>Private Link를 사용하여 Azure Synapse 작업 영역에 연결

이 문서에서는 Azure Synapse 작업 영역에 대한 프라이빗 엔드포인트를 만드는 방법을 설명합니다. 자세히 알아보려면 [프라이빗 링크 및 프라이빗 엔드포인트](../../private-link/index.yml)를 참조하세요.

## <a name="step-1-register-network-resource-provider"></a>1단계: 네트워크 리소스 공급자 등록

아직 등록하지 않은 경우 네트워크 리소스 공급자를 등록합니다. 리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다. [등록](../../azure-resource-manager/management/resource-providers-and-types.md)할 때 리소스 공급자 목록에서 *Microsoft.Network* 를 선택합니다. 네트워크 리소스 공급자가 이미 등록되어 있는 경우 2단계를 진행합니다.

## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>2단계: Azure Portal에서 Azure Synapse 작업 영역 열기

**보안** 에서 **프라이빗 엔드포인트 연결** 을 선택합니다. 
![Azure Portal에서 Azure Synapse 작업 영역 열기](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

다음 화면에서 **+ 프라이빗 엔드포인트** 를 선택합니다.

![Azure Portal에서 프라이빗 엔드포인트 열기](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1a.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>3단계: 구독 및 지역 세부 정보 선택

**프라이빗 엔드포인트 만들기** 창의 **기본 사항** 탭에서 **구독** 을 선택하고 **리소스 그룹** 을 선택합니다. 만들려는 프라이빗 엔드포인트에 **이름** 을 제공합니다. 프라이빗 엔드포인트를 만들 **지역** 을 선택합니다.

프라이빗 엔드포인트는 서브넷에 생성됩니다. 선택한 구독, 리소스 그룹 및 지역이 프라이빗 엔드포인트 서브넷을 필터링합니다. 완료되면 **다음: 리소스 >** 를 선택합니다.
![구독 및 지역 세부 정보 선택 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>4단계: Azure Synapse 작업 영역 세부 정보 선택

**리소스** 탭에서 **내 디렉터리의 Azure 리소스에 연결** 을 선택합니다. Azure Synapse 작업 영역을 포함하는 **구독** 을 선택합니다. Azure Synapse 작업 영역에 대한 프라이빗 엔드포인트를 만들기 위한 **리소스 종류** 는 *Microsoft.Synapse/workspaces* 입니다.

**리소스** 로 Azure Synapse 작업 영역을 선택합니다. 모든 Azure Synapse 작업 영역에는 다음에 대한 프라이빗 엔드포인트를 만들 수 있는 세 개의 **대상 하위 리소스** 가 있습니다. Sql, SqlOnDemand 및 Dev
- Sql는 SQL 풀에서 SQL 쿼리 실행 시 사용합니다.
- SqlOnDemand는 SQL 기본 제공 쿼리 실행 시 사용합니다.
- Dev는 Azure Synapse Analytics Studio 작업 영역 내에서 다른 모든 항목에 액세스할 때 사용합니다.

완료되면 **다음: 구성>** 을 선택하여 설정의 다음 부분으로 이동합니다.
![구독 및 지역 세부 정보 선택 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

**구성** 탭에서 **가상 네트워크** 를 선택하고 프라이빗 엔드포인트를 만들어야 하는 **서브넷** 을 선택합니다. 또한 프라이빗 엔드포인트에 매핑되는 DNS 레코드를 만들어야 합니다.

**프라이빗 DNS 영역과 통합** 에 대해 **예** 를 선택하여 프라이빗 엔드포인트를 프라이빗 DNS 영역과 통합합니다. Microsoft Azure Virtual Network에 연결된 프라이빗 DNS 영역이 없는 경우 새 프라이빗 DNS 영역을 만듭니다. 완료되면 **검토 + 만들기** 를 선택합니다.

![구독 및 지역 세부 정보 선택 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

배포가 완료되면 Azure Portal에서 Azure Synapse 작업 영역을 열고 **프라이빗 엔드포인트 연결** 을 선택합니다. 프라이빗 엔드포인트와 연결된 새 프라이빗 엔드포인트 및 프라이빗 엔드포인트 연결 이름이 표시됩니다.

![구독 및 지역 세부 정보 선택 4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>다음 단계

[관리형 작업 영역 가상 네트워크](./synapse-workspace-managed-vnet.md)에 대한 자세한 정보를 알아봅니다

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보

[데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)
