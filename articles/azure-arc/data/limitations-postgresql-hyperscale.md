---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일의 제한 사항
description: Azure Arc 지원 PostgreSQL 하이퍼스케일의 제한 사항
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: a2a63e62598b291388375e0eb520d390dbdc4278
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562830"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일의 제한 사항

이 문서에서는 Azure Arc 지원 PostgreSQL 하이퍼스케일의 제한 사항을 설명합니다. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>백업 및 복원
디자인 및 환경을 마무리 하면 백업/복원 기능이 일시적으로 제거 되었습니다.

## <a name="high-availability"></a>고가용성
인프라 오류가 발생 하는 경우 고가용성을 구성 하 고 장애 조치 (failover)를 보장할 수 없습니다.

## <a name="databases"></a>데이터베이스
여러 작업자 노드에서 배포를 확장 한 경우 서버 그룹에서 둘 이상의 데이터베이스를 호스트 하는 것은 지원 되지 않습니다.

## <a name="roles-and-responsibilities"></a>역할 및 책임

Microsoft와 고객 간의 역할 및 책임은 Azure 관리 서비스 (Platform As A Service 또는 PaaS)와 Azure 하이브리드 (예: Azure Arc 사용 PostgreSQL Hyperscale) 사이에서 다릅니다. 

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

- **사용해보기.** AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), Google GKE(Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc 빠른 시작](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)을 사용하여 빠르게 시작하세요. 

- **나만의 인프라 생성.** 고유한 Kubernetes 클러스터에 만들려면 다음 단계를 따르세요. 
   1. [클라이언트 도구 설치](install-client-tools.md)
   2. [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)
   3. [Azure Arc에 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc 지원 데이터 서비스에 대해 자세히 알아보기](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc에 대해 자세히 알아보세요.](https://aka.ms/azurearc)
