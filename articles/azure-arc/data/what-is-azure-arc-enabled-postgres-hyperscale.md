---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일이란?
description: Azure Arc 지원 PostgreSQL 하이퍼스케일이란?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: d43753084f79184b338b16223ed45cb00a2dfb33
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732461"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일이란?

Azure Arc 지원 PostgreSQL 하이퍼스케일은 Azure Arc 지원 데이터 서비스의 일부로 사용 가능한 데이터베이스 서비스 중 하나입니다. Azure Arc를 통해 선택한 인프라 및 Kubernetes를 사용하여 온-프레미스, 에지 및 퍼블릭 클라우드 환경의 Azure 데이터 서비스를 실행할 수 있습니다. Azure Arc 지원 데이터 서비스의 가치 제안에서 명시하고 있는 가치는 다음과 같습니다.
- 항상 최신
- 탄력적 확장
- 셀프서비스 프로비저닝
- 통합 관리
- 연결되지 않은 시나리오 지원

자세한 내용은 다음을 참조하세요.
- [Azure Arc 지원 데이터 서비스란?](overview.md)
- [연결 모드 및 요구 사항](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

관련 기능에 대한 자세한 내용은 노출되는 데이터 에피소드를 참조할 수도 있습니다.
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]

## <a name="compare-solutions"></a>솔루션 비교

이 섹션에서는 Azure Arc 지원 PostgreSQL 하이퍼스케일이 Azure Database for PostgreSQL 하이퍼스케일(Citus)과 어떻게 다른지 설명합니다.

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL 하이퍼스케일(Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database for PostgreSQL 하이퍼스케일(Citus)":::

Azure(PaaS)에서 Database as a Service로 제공되는 Postgres 데이터베이스 엔진의 하이퍼스케일 폼 팩터입니다. 하이퍼스케일 환경을 사용하는 Citus 확장으로 구동됩니다. 이 폼 팩터에서 서비스는 Microsoft 데이터 센터에서 실행되며 Microsoft가 운영합니다.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc 지원 PostgreSQL 하이퍼스케일":::

Azure Arc 지원 데이터 서비스와 함께 제공되는 Postgres 데이터베이스 엔진의 하이퍼스케일 폼 팩터입니다. 하이퍼스케일 환경을 사용하는 Citus 확장으로도 구동됩니다. 이 폼 팩터에서 고객은 시스템을 호스트하고 운영하는 인프라를 제공합니다.

## <a name="next-steps"></a>다음 단계
- **사용해보기.** AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), Google GKE(Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc 빠른 시작](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)을 사용하여 빠르게 시작하세요. 

- **나만의 인프라 생성.** 고유한 Kubernetes 클러스터에 만들려면 다음 단계를 따르세요. 
   1. [클라이언트 도구 설치](install-client-tools.md)
   2. [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)
   3. [Azure Arc에 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc 지원 데이터 서비스에 대해 자세히 알아보기](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc에 대해 자세히 알아보세요.](https://aka.ms/azurearc)
