---
title: 새로운 기능은 무엇입니까?
description: Azure Synapse Analytics 대한 새로운 기능 및 설명서 개선에 대해 알아봅니다.
services: synapse-analytics
author: ryanmajidi
ms.author: rymajidi
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 3748e969e83d651fa76dc9cff9b92b2f2ffc4f1d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483072"
---
# <a name="whats-new-in-azure-synapse-analytics"></a>Azure Synapse Analytics 새로운 내용

이 문서에서는 Azure Synapse Analytics 대한 업데이트를 설명하고 현재 공개 미리 보기로 제공된 기능을 나열합니다.

목록의 각 기능 옆에 있는 링크는 다음과 같습니다.

- [blog]: Most features are explained in a section in the monthly update blog post.
- [article]: Some features have an article that provides more detail
- 나머지 기능은 설명이 필요 없고 문서가 필요하지 않습니다.

## <a name="october-2021-update"></a>2021년 10월 업데이트

이번 달 Azure Synapse Analytics 새로운 업데이트는 다음과 같습니다.

### <a name="general"></a>일반

- Azure Synapse 사전 구매 계획을 통해 비용 관리 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#manage-cost) [문서](../cost-management-billing/reservations/synapse-analytics-pre-purchase-plan.md)
- Azure 지역 간 Azure Synapse 작업 영역 이동 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#move-workspace-region) [문서](how-to-move-workspace-from-one-region-to-another.md)

### <a name="apache-spark-for-synapse"></a>Synapse용 Apache Spark

- Spark 성능 최적화 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#spark-performance) 

### <a name="security"></a>보안

- 이제 모든 Synapse RBAC 역할을 프로덕션 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#synapse-rbac) [문서에서](./security/synapse-workspace-synapse-rbac-roles.md) 일반적으로 사용할 수 있습니다.
- 이중 암호화를 위한 User-Assigned 관리 ID 활용 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#user-assigned-managed-identities) [문서](./security/workspaces-encryption.md)
- 이제 Synapse 관리자가 전용 SQL 풀에 대한 상승된 액세스 권한을 갖습니다. [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#elevated-access) [문서](./security/synapse-workspace-access-control-overview.md)
    
###  <a name="governance"></a>거버넌스

- 이제 Synapse 작업 영역에서 계보 데이터를 Azure Purview에 자동으로 푸시할 수 [있습니다. 블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#synapse-purview-lineage) [문서](../purview/how-to-lineage-azure-synapse-analytics.md)
 
### <a name="integrate"></a>통합

- 데이터 흐름에서 Stringify를 사용하여 복잡한 데이터 형식을 문자열로 쉽게 변환 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#stringify-transform) [문서](../data-factory/data-flow-stringify.md)
- 데이터 흐름에서 Spark 세션 TTL(TTL) 제어 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#data-flowspark-ttl) [문서](../data-factory/concepts-integration-runtime-performance.md)

### <a name="cicd--git"></a>CI/CD & Git

- GitHub 작업을 사용하여 Synapse 작업 영역 배포 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#deploy-synapse-github-action) [문서](./cicd/continuous-integration-delivery.md#configure-github-actions-secrets)
- Synapse Studio [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#create-git-branch-in-studio) [문서에서](./cicd/source-control.md#creating-feature-branches) Git 분기 만들기에 대한 추가 제어

### <a name="developer-experience"></a>개발자 환경

- Synapse Notebooks 미리 보기 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#notebook-markdown-toolbar) [문서의](./spark/apache-spark-development-using-notebooks.md) 향상된 Markdown 편집
- Pandas 데이터 프레임이 형식이 잘 지정된 HTML 테이블로 자동으로 렌더링 [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#pandas-dataframe-html) [문서](./spark/apache-spark-data-visualization.md)
- Synapse Notebooks [블로그](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#notebook-ipythong-widgets) [문서에서](./spark/apache-spark-development-using-notebooks.md) IPython 위젯 사용
- 이제 Python 및 [Scala에](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#mssparkutils-context) 사용할 수 있는 Mssparkutils 런타임 컨텍스트 블로그 [문서](./spark/microsoft-spark-utilities.md?pivots=programming-language-python)

## <a name="next-steps"></a>다음 단계

[Azure Synapse Analytics 시작](get-started.md)


