---
title: Azure Arc 사용 SQL Managed Instance 제한 사항
description: Azure Arc 사용 SQL Managed Instance 제한 사항
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 09/07/2021
ms.topic: conceptual
ms.openlocfilehash: 8709504d93f22ee5f2704b7b1a8d71a5e66df796
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838754"
---
# <a name="limitations-of-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 사용 SQL Managed Instance 제한 사항

이 문서에서는 Azure Arc 지원 SQL Managed Instance 제한에 대해 설명합니다. 

현재까지 중요 비즈니스용 서비스 계층은 공개 미리 보기 상태입니다. 범용 서비스 계층은 일반 공급 상태입니다.

## <a name="backup-and-restore"></a>백업 및 복원

### <a name="automated-backups"></a>자동화된 백업 

-  단순 복구 모델을 사용하는 사용자 데이터베이스는 백업되지 않습니다.
-  시스템 데이터베이스 `model`은 데이터베이스 만들기/삭제 간섭을 방지하기 위해 백업되지 않습니다. 관리자 작업이 수행되면 데이터베이스가 잠깁니다.

### <a name="point-in-time-restore-pitr"></a>PITR(특정 시점 복원)

-  한 Azure Arc 지원 SQL Managed Instance 다른 Azure Arc 지원 SQL Managed Instance 복원을 지원하지 않습니다.  데이터베이스는 백업이 만들어진 동일한 Arc 지원 SQL Managed Instance로만 복원할 수 있습니다.
-  특정 시점 복원을 위해 데이터베이스 이름 바꾸기는 현재 지원되지 않습니다.
-  현재 TDE 사용 데이터베이스 복원은 지원되지 않습니다.
-  삭제된 데이터베이스는 현재 복원할 수 없습니다.

## <a name="other-limitations"></a>기타 제한 사항 

-  트랜잭션 복제는 현재 지원되지 않습니다.
-  현재 로그 전달이 차단되었습니다.
-  SQL Server 인증만 지원됩니다.

## <a name="roles-and-responsibilities"></a>역할 및 책임

Microsoft와 고객 간의 역할과 책임은 Azure PaaS 서비스(Platform as A Service)와 Azure 하이브리드(예: Azure Arc 지원 SQL Managed Instance) 간에 다릅니다. 

### <a name="frequently-asked-questions"></a>질문과 대답

아래 표에는 지원 역할 및 책임에 대한 자주 묻는 질문이 요약되어 있습니다.

| 질문                          | Azure PaaS(Platform As A Service) | Azure Arc 하이브리드 서비스 |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| 누가 인프라를 제공하나요?  | Microsoft                          | Customer                  |
| 누가 소프트웨어를 제공하나요?*       | Microsoft                          | Microsoft                 |
| 누가 작업을 수행하나요?          | Microsoft                          | Customer                  |
| Microsoft에서 SLA를 제공하나요?      | 예                                | 예                        |
| 누가 SLA를 부과하나요?          | Microsoft                          | Customer                  |

\* Azure 서비스

__Microsoft에서 Azure Arc 하이브리드 서비스에 대한 SLA를 제공하지 않는 이유는 무엇인가요?__ Microsoft는 인프라를 소유하지 않고 운영하지 않기 때문입니다. 고객이 인프라를 소유하고 운영합니다.

## <a name="next-steps"></a>다음 단계

- **사용해보기.** AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), Google GKE(Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc 빠른 시작](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)을 사용하여 빠르게 시작하세요. 

- **나만의 인프라 생성.** 고유한 Kubernetes 클러스터에 만들려면 다음 단계를 따르세요. 
   1. [클라이언트 도구 설치](install-client-tools.md)
   2. [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)
   3. [Azure Arc 지원 SQL Managed Instance 만들기](create-sql-managed-instance.md) 

- **Learn**
   - [Azure Arc 지원 데이터 서비스에 대해 자세히 알아보기](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc에 대해 자세히 알아보세요.](https://aka.ms/azurearc)
