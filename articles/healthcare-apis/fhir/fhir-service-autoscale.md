---
title: Azure 의료 Api FHIR 서비스에 대 한 자동 크기 조정 기능
description: 이 문서에서는 Azure 의료 Api FHIR 서비스에 대 한 자동 크기 조정 기능을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: zxue
ms.openlocfilehash: 382a3ccee6f14c4d95e74bb40a8d899868754926
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814761"
---
# <a name="fhir-service-autoscale"></a>서비스 자동 크기 조정

Azure 의료 Api의 FHIR 서비스는 고객이 FHIR 규격 의료 데이터를 유지 하 고 API 서비스 끝점을 통해 안전 하 게 상호 작용할 수 있도록 하는 관리 서비스입니다. FHIR 서비스는 다양 한 워크 로드를 충족 하는 기본 제공 자동 크기 조정 기능을 제공 합니다.  

## <a name="what-is-fhir-service-autoscale"></a>FHIR 서비스 자동 크기 조정 이란?   

FHIR 서비스에 대 한 자동 크기 조정 기능은 사용자가 언제 든 지 일관 되거나 다양 한 작업으로 데이터 트랜잭션을 수행할 때 지원 되는 서비스 확장성을 자동으로 제공 하도록 설계 되었습니다. FHIR 서비스가 지원 되는 모든 지역에서 사용할 수 있습니다. 자동 크기 조정 기능은 Azure 지역에서 사용할 수 있는 리소스에 따라 달라 집니다.   

## <a name="how-does-fhir-service-autoscale-work"></a>FHIR 서비스 자동 크기 조정은 어떻게 작동 하나요?  

트랜잭션 워크 로드가 높으면 자동 크기 조정 기능을 통해 컴퓨팅 리소스가 자동으로 증가 합니다. 트랜잭션 워크 로드가 낮으면 컴퓨팅 리소스를 적절 하 게 줄입니다.  

자동 크기 조정 기능은 컴퓨팅 리소스를 자동으로 조정 하 여 전반적인 서비스 확장성을 최적화 합니다. 환자 ID를 사용 하 여 환자 정보 가져오기와 같은 간단한 쿼리 및 이름이 "Sarah" 인 환자에 대 한 모든 리소스 가져오기와 같은 고급 쿼리를 포함 하는 읽기 요청을 수행 하는 경우 `DiagnosticReport` 나 FHIR 리소스를 만들거나 업데이트 하는 경우 자동 크기 조정 기능은 리소스 할당의 dynamics 및 복잡성을 관리 하 여 높은 확장성을 보장 합니다.

자동 크기 조정 기능은 관리 서비스의 일부 이며 고객의 작업은 필요 하지 않습니다. 그러나 고객은이 기능을 개선 하는 데 도움이 되도록 피드백을 공유 하는 것이 좋습니다. 고객이 경험 했을 수 있는 모든 확장성 문제를 해결 하기 위해 지원 티켓을 올릴 수도 있습니다.  

### <a name="what-is-the-cost-of-the-fhir-service-autoscale"></a>FHIR 서비스 자동 크기 조정의 비용은 얼마 인가요?  

자동 크기 조정 기능은 새 API 청구 모델을 기반으로 고객에 게 추가 비용 없이 발생 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 의료 Api의 FHIR 서비스 자동 크기 조정 기능에 대해 알아보았습니다. FHIR 서비스 지원 기능에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[지원 되는 FHIR 기능](fhir-features-supported.md)
