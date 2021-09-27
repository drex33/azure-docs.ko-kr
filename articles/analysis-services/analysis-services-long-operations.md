---
title: Azure Analysis Services의 장기 실행 작업에 대한 모범 사례 | Microsoft Docs
description: 이 문서에서는 장기 실행 작업에 대한 모범 사례를 설명합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: owend
ms.openlocfilehash: 65d1b6cbee1040846fec2d8e1dd8153eedf9eabc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580968"
---
# <a name="best-practices-for-long-running-operations"></a>장기 실행 작업에 대한 모범 사례

Azure Analysis Services에서 *노드* 는 서버 리소스가 실행되는 호스트 가상 머신을 나타냅니다. 장기 실행 쿼리, 새로 고침 작업 및 쿼리 스케일 아웃 동기화와 같은 일부 작업은 서버 리소스가 다른 노드로 이동하는 경우 실패할 수 있습니다. 이 시나리오의 일반적인 오류 메시지는 다음과 같습니다.

- "장기 실행 XMLA 요청을 찾으려고 시도하는 동안 오류가 발생했습니다. 서비스를 업그레이드하거나 서버를 다시 시작하여 요청이 중단되었을 수 있습니다. "
- “모델 '\<database\>'에 대한 ID '\<guid\>' 작업이 서비스 오류(비활동)로 인해 '업데이트 없이 중단되었으므로 새로 고침 요청을 취소하는 중' 메시지와 함께 취소되었습니다. 이는 내부 서비스 문제입니다. 이 문제가 반복적으로 발생하는 경우 도움을 받으려면 작업을 다시 제출하거나 티켓을 제출하세요."

장기 실행 작업이 중단될 수 있는 많은 이유가 있습니다. 예를 들어 Azure의 업데이트는 다음과 같습니다. 
- 운영 체제 패치 
- 보안 업데이트
- Azure Analysis Services 서비스 업데이트
- Service Fabric 업데이트입니다. Service Fabric은 Azure Analysis Services를 포함하여 다양한 Microsoft 클라우드 서비스에서 사용하는 플랫폼 구성 요소입니다.

서비스에서 발생하는 업데이트 외에도 부하 분산으로 인해 노드 간에 서비스가 자연스럽게 이동합니다. 노드 이동은 클라우드 서비스의 예상되는 부분입니다. Azure Analysis Services는 노드 이동의 영향을 최소화하려고 하지만 완전히 제거할 수는 없습니다. 

노드 이동 외에도 다른 오류가 발생할 수 있습니다. 예를 들어 데이터 원본 데이터베이스 시스템이 오프라인 상태이거나 네트워크 연결이 끊어질 수 있습니다. 새로 고침 중이고 파티션에 1000만 개의 행이 있으며 900만 번째 행에서 오류가 발생하는 경우 실패 지점에서 새로 고침을 다시 시작할 방법이 없습니다. 서비스를 처음부터 다시 시작해야 합니다. 

## <a name="refresh-rest-api"></a>REST API 새로 고침

데이터 새로 고침과 같은 장기 실행 작업의 경우 서비스 중단이 어려울 수 있습니다. Azure Analysis Services에는 서비스 중단의 부정적 영향을 완화하는 데 도움이 되는 REST API가 포함되어 있습니다. [REST API를 사용한 비동기 새로 고침](analysis-services-async-refresh.md)을 참조합니다.
 
REST API 외에도 장기 실행 새로 고침 작업 중에 잠재적인 문제를 최소화하는 데 사용할 수 있는 다른 방법이 있습니다. 주요 목표는 새로 고침 작업을 처음부터 다시 시작하지 않고 대신에 단계적으로 커밋할 수 있는 더 작은 단위로 새로 고침을 수행하는 것입니다. 
 
REST API는 이러한 다시 시작을 허용하지만 파티션 생성 및 삭제의 완전한 유연성을 허용하지 않습니다. 시나리오에 복잡한 데이터 관리 작업이 필요한 경우 솔루션은 논리적 일괄 처리 형태를 포함해야 합니다. 예를 들어 트랜잭션을 사용하여 데이터를 다양한 개별 일괄 처리로 처리하면 실패 발생 시 처음부터 다시 시작할 필요 없이 중간 검사점에서 시작할 수 있습니다. 
 
## <a name="scale-out-query-replicas"></a>스케일 아웃 쿼리 복제본

REST를 사용하든 사용자 지정 논리를 사용하든 관계없이 일괄 처리가 진행되는 동안 클라이언트 애플리케이션 쿼리에서 일관되지 않은 결과나 중간 결과를 반환할 수 있습니다. 처리가 진행되는 동안 클라이언트 애플리케이션 쿼리에서 반환한 일관된 데이터가 필요하고 모델 데이터가 중간 상태이면 읽기 전용 쿼리 복제본과 함께 [스케일 아웃](analysis-services-scale-out.md) 쿼리 복제본을 사용할 수 있습니다.

읽기 전용 쿼리 복제본을 사용하면 새로 고침이 일괄 처리로 수행되는 동안 클라이언트 애플리케이션 사용자가 읽기 전용 복제본에 대한 이전 데이터 스냅샷을 계속 쿼리할 수 있습니다. 새로 고침이 완료되면 읽기 전용 복제본을 최신 상태로 만들기 위해 동기화 작업을 수행할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[REST API를 사용한 비동기 새로 고침](analysis-services-async-refresh.md)  
[Azure Analysis Services 규모 확장](analysis-services-scale-out.md)  
[Analysis Services 고가용성](analysis-services-bcdr.md)  
[Azure 서비스에 대한 다시 시도 지침](/azure/architecture/best-practices/retry-service-specific)