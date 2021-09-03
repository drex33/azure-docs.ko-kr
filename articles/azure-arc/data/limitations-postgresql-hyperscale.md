---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일의 제한 사항
description: Azure Arc 지원 PostgreSQL 하이퍼스케일의 제한 사항
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 4fdcf88200b3c2a6b5371633c10d9246cfbf4c31
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730247"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일의 제한 사항

이 문서에서는 Azure Arc 지원 PostgreSQL 하이퍼스케일의 제한 사항을 설명합니다. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>백업 및 복원

- 동일한 서버 그룹의 특정 날짜 및 시간으로 복원하는 것과 같은 특정 시점 복원은 지원되지 않습니다. 특정 시점 복원을 수행하는 경우 복원하기 전에 배포한 다른 서버 그룹에서 복원해야 합니다. 새 서버 그룹으로 복원한 후 원본 서버 그룹을 삭제할 수 있습니다.
- PostgreSQL 버전 12에 대해 백업의 전체 콘텐츠를 동일한 서버 그룹으로 복원(특정 시점으로 복원하는 것과 반대로)할 수 없습니다. 타임라인이 포함된 PostgreSQL 엔진의 제한으로 인해 PostgreSQL 버전 11은 지원되지 않습니다. PostgreSQL 서버 그룹 버전 11에 대한 백업의 전체 콘텐츠를 복원하려면 다른 서버 그룹으로 복원해야 합니다.


## <a name="databases"></a>데이터베이스

서버 그룹에서 둘 이상의 데이터베이스를 호스팅할 수 없습니다.


## <a name="security"></a>보안

사용자 및 역할 관리는 지원되지 않습니다. 지금은 postgres 표준 사용자를 계속 사용합니다.

## <a name="roles-and-responsibilities"></a>역할 및 책임

Microsoft와 고객 간의 역할과 책임은 Azure PaaS 서비스(Platform As A Service)와 Azure 하이브리드(예: Azure Arc 지원 PostgreSQL 하이퍼스케일) 간에 서로 다릅니다. 

### <a name="frequently-asked-questions"></a>질문과 대답

아래 표에는 지원 역할 및 책임에 대한 자주 묻는 질문이 요약되어 있습니다.

| 질문                      | Azure PaaS(Platform As A Service) | Azure Arc 하이브리드 서비스 |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| 누가 인프라를 제공하나요?  | Microsoft                          | Customer                  |
| 누가 소프트웨어를 제공하나요?*       | Microsoft                          | Microsoft                 |
| 누가 작업을 수행하나요? | Microsoft                          | Customer                  |
| Microsoft에서 SLA를 제공하나요?      | 예                                | 예                        |
| 누가 SLA를 부과하나요? | Microsoft                          | Customer                  |

\* Azure 서비스

__Microsoft에서 Azure Arc 하이브리드 서비스에 대한 SLA를 제공하지 않는 이유는 무엇인가요?__ Microsoft는 인프라를 소유하지 않고 운영하지 않기 때문입니다. 고객이 인프라를 소유하고 운영합니다.

## <a name="next-steps"></a>다음 단계

- **사용해보기.** AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), Google GKE(Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc 빠른 시작](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)을 사용하여 빠르게 시작하세요. 

- **나만의 인프라 생성.** 고유한 Kubernetes 클러스터에 만들려면 다음 단계를 따르세요. 
   1. [클라이언트 도구 설치](install-client-tools.md)
   2. [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)
   3. [Azure Arc에 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc 지원 데이터 서비스에 대해 자세히 알아보기](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc에 대해 자세히 알아보세요.](https://aka.ms/azurearc)
