---
title: Azure Front Door의 백 엔드 및 백 엔드 풀 | Microsoft Docs
description: 이 문서에서는 Front Door 구성에 있는 백 엔드 및 백 엔드 풀에 대해 설명합니다.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 68feb1c2df14c3325ae74ef36874e7008bf9d108
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371033"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Azure Front Door의 백 엔드 및 백 엔드 풀
이 문서에서는 Azure Front Door를 사용하여 웹 애플리케이션 배포를 매핑하는 방법에 대한 개념을 설명합니다. 또한 애플리케이션 백 엔드와 관련된 Front Door 구성에서 사용되는 다양한 용어를 설명합니다.

## <a name="backends"></a>백 엔드
백 엔드는 한 지역의 웹 애플리케이션 배포를 나타냅니다. Front Door는 백 엔드 풀의 Azure 리소스와 비 Azure 리소스를 모두 지원합니다. 애플리케이션은 온-프레미스 데이터 센터에 있거나 다른 클라우드 공급자에 있을 수 있습니다.

Front Door 백 엔드는 클라이언트 요청을 처리하는 애플리케이션의 호스트 이름 또는 공용 IP를 나타냅니다. 백 엔드를 데이터베이스 계층, 스토리지 계층 등과 혼동해서는 안 됩니다. 백 엔드는 애플리케이션 백 엔드의 퍼블릭 엔드포인트로 표시되어야 합니다. Front Door 백 엔드 풀에 백 엔드를 추가하는 경우 다음도 추가해야 합니다.

- **백 엔드 호스트 유형**. 추가할 리소스의 유형입니다. Front Door는 앱 서비스, 클라우드 서비스 또는 스토리지에서 앱 백 엔드를 자동으로 검색하도록 지원합니다. Azure 또는 비 Azure 백 엔드의 다른 리소스를 원할 경우 **사용자 지정 호스트** 를 선택합니다.

    >[!IMPORTANT]
    >구성하는 동안 API는 Front Door 환경에서 백 엔드에 액세스할 수 없는지 확인하지 않습니다. Front Door가 백 엔드에 연결할 수 있는지 확인합니다.

- **구독 및 백 엔드 호스트 이름**. 백 엔드 호스트 유형으로 **사용자 지정 호스트** 를 선택하지 않은 경우, UI에서 적절한 구독과 해당 백 엔드 호스트 이름을 선택하여 백 엔드를 선택합니다.

- **백 엔드 호스트 헤더**. 각 요청에 대해 백 엔드로 전송된 호스트 헤더 값입니다. 자세한 내용은 [백 엔드 호스트 헤더](#hostheader)를 참조하세요.

- **우선 순위**. 모든 트래픽에 기본 서비스 백 엔드를 사용하려면 각 백 엔드에 우선 순위를 할당합니다. 또한 기본 또는 백업 백 엔드를 사용할 수 없는 경우 백업을 제공합니다. 자세한 내용은 [우선 순위](front-door-routing-methods.md#priority)를 참조하세요.

- **가중치**. 백 엔드 집합에서 균일하게 또는 가중 계수에 따라 트래픽을 분산하려면 각 백 엔드에 가중치를 할당합니다. 자세한 내용은 [가중치](front-door-routing-methods.md#weighted)를 참조하세요.

### <a name="backend-host-header"></a><a name = "hostheader"></a>백 엔드 호스트 헤더

Front Door에서 백 엔드로 전달된 요청에는 백 엔드가 대상 리소스를 검색하는 데 사용하는 호스트 헤더 필드가 포함됩니다. 일반적으로 이 필드의 값은 호스트 헤더와 포트가 있는 백 엔드 URI에서 가져옵니다.

예를 들어 `www.contoso.com`에 대한 요청에는 호스트 헤더 www.contoso.com이 있습니다. Azure Portal을 사용하여 백 엔드를 구성하는 경우 이 필드의 기본값은 백 엔드의 호스트 이름입니다. 백 엔드가 contoso-westus.azurewebsites.net인 경우 Azure Portal에서 백 엔드 호스트 헤더에 자동으로 채워지는 값은 contoso-westus.azurewebsites.net입니다. 그러나 이 필드를 명시적으로 설정하지 않고 Azure Resource Manager 템플릿 또는 다른 방법을 사용하는 경우, Front Door는 들어오는 호스트 이름을 호스트 헤더 값으로 보냅니다. www\.contoso.com에 대한 요청이 수행되고 백 엔드가 빈 헤더 필드를 포함하는 contoso-westus.azurewebsites.net인 경우 Front Door는 호스트 헤더를 www\.contoso.com으로 설정합니다.

대부분의 앱 백 엔드(Azure Web Apps, Blob 스토리지, Cloud Services)에서는 호스트 헤더가 백 엔드의 도메인과 일치해야 합니다. 그러나 백 엔드로 라우팅하는 프런트 엔드 호스트는 `www.contoso.net`과 같은 다른 호스트 이름을 사용합니다.

백 엔드에서 백 엔드 호스트 이름과 일치하는 호스트 헤더를 요구하는 경우 백 엔드 호스트 헤더에 백 엔드의 호스트 이름이 포함되어 있는지 확인합니다.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>백 엔드에 대한 백 엔드 호스트 헤더 구성

백 엔드 풀 섹션에서 백 엔드의 **백 엔드 호스트 헤더** 필드를 구성하려면 다음을 수행합니다.

1. Front Door 리소스를 열고 구성할 백 엔드가 있는 백 엔드 풀을 선택합니다.

2. 아직 백 엔드를 추가하지 않은 경우 추가하거나 기존 백 엔드를 편집합니다.

3. 백 엔드 호스트 헤더 필드를 사용자 지정 값으로 설정하거나 비워 둡니다. 들어오는 요청의 호스트 이름은 호스트 헤더 값으로 사용됩니다.

## <a name="backend-pools"></a>백 엔드 풀
Front Door의 백 엔드 풀은 앱을 위해 유사한 트래픽을 수신하는 백 엔드 집합을 나타냅니다. 즉, 전 세계에서 동일한 트래픽을 수신하고 예상 동작으로 응답하는 앱 인스턴스의 논리적 그룹입니다. 백 엔드는 일반적으로 여러 다른 지역 또는 동일한 지역 내에서 배포됩니다. 모든 백 엔드는 활성/활성 배포 모드이거나 활성/수동 구성으로 정의될 수 있습니다.

백 엔드 풀은 상태 프로브를 통해 여러 백 엔드를 평가하는 방법을 정의합니다. 또한 부하 분산이 해당 항목 간에 발생하는 방식을 정의합니다.

### <a name="health-probes"></a>상태 프로브
Front Door는 구성된 각 백 엔드에 주기적으로 HTTP/HTTPS 프로브 요청을 보냅니다. 프로브 요청은 최종 사용자 요청의 부하를 분산하기 위해 각 백 엔드의 근접성과 상태를 결정합니다. 백 엔드 풀의 상태 프로브 설정은 앱 백 엔드의 상태를 폴링하는 방법을 정의합니다. 부하 분산 구성에 사용할 수 있는 설정은 다음과 같습니다.

- **경로**: 백 엔드 풀의 모든 백 엔드에 대한 프로브 요청에 사용되는 URL입니다. 예를 들어 백 엔드 중 하나가 contoso-westus.azurewebsites.net이고 경로가 /probe/test.aspx로 설정된 경우 Front Door 환경은 프로토콜이 HTTP로 설정되었다고 가정하고 http\://contoso-westus.azurewebsites.net/probe/test.aspx로 상태 프로브 요청을 보냅니다.

- **프로토콜**: HTTP 또는 HTTPS 프로토콜을 사용하여 Front Door에서 백 엔드로 상태 프로브 요청을 보낼지 여부를 정의합니다.

- **메서드**: 상태 프로브를 보내는 데 사용할 HTTP 메서드입니다. 옵션에는 GET 또는 HEAD(기본값)가 포함됩니다.
    > [!NOTE]
    > 백 엔드의 부하 및 비용을 낮추기 위해 Front Door는 상태 프로브에 HEAD 요청을 사용하도록 권장합니다.

- **간격(초)** : 백 엔드에 대한 상태 프로브의 빈도, 즉 각 Front Door 환경에서 프로브를 전송하는 간격을 정의합니다.

    >[!NOTE]
    >더 빠른 장애 조치(failover)를 구현하려면 간격을 더 낮은 값으로 설정합니다. 값이 낮을수록 백 엔드가 수신하는 상태 프로브 용량이 더 많아집니다. 예를 들어 전체적으로 100 Front Door POP에서 간격이 30초로 설정되었다면 각 백 엔드는 분당 200개 프로브 요청을 수신합니다.

자세한 내용은 [상태 프로브](front-door-health-probes.md)를 참조하세요.

### <a name="load-balancing-settings"></a>부하 분산 설정
백 엔드 풀의 부하 분산 설정은 상태 프로브를 평가하는 방법을 정의합니다. 이 설정은 백엔드가 정상인지 비정상인지를 결정합니다. 또한 백 엔드 풀의 서로 다른 백 엔드 간에 트래픽 부하를 분산하는 방법을 확인합니다. 부하 분산 구성에 사용할 수 있는 설정은 다음과 같습니다.

- **샘플 크기**. 백 엔드 상태 평가에서 고려해야 하는 상태 프로브의 샘플 수를 식별합니다.

- **성공적인 샘플 크기**. 앞에서 설명한 대로 샘플 크기, 즉 백 엔드를 정상이라고 부르기 위해 필요한 성공적인 샘플 수를 정의합니다. 예를 들어 Front Door 상태 프로브 간격은 30초이고, 샘플 크기는 5이고, 성공적인 샘플 크기는 3이라고 가정합니다. 백 엔드에 대한 상태 프로브를 평가할 때마다 지난 5개 샘플을 150초(5 x 30) 동안 살펴봅니다. 백 엔드를 정상으로 선언하려면 적어도 성공적인 프로브가 3개 필요합니다.

- **대기 시간 민감도(추가 대기 시간)** . Front Door가 대기 시간 측정 민감도 범위 내에 요청을 백 엔드로 보낼지, 아니면 가장 가까운 백 엔드로 요청을 전달할지를 정의합니다.

자세한 내용은 [최소 대기 시간 기반 라우팅 방법](front-door-routing-methods.md#latency)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Front Door 프로필 만들기](quickstart-create-front-door.md)
- [Front Door 작동 원리](front-door-routing-architecture.md)
