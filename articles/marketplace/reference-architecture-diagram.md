---
title: 참조 아키텍처 다이어그램 | Azure Marketplace
description: Microsoft 상업용 Marketplace에서 제품에 대한 참조 아키텍처 다이어그램을 만드는 방법입니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/22/2021
ms.openlocfilehash: 63c820a7dd4bcbe1322478a46043963edc45dd2b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129054265"
---
# <a name="reference-architecture-diagram"></a>참조 아키텍처 다이어그램

참조 아키텍처 다이어그램은 제품에서 사용하는 인프라를 나타내는 모델입니다. Azure IP 솔루션의 경우 다이어그램은 제품에서 IP 공동 판매의 기술 요구 사항에 따라 Microsoft의 클라우드 서비스를 사용하는 방법도 보여줘야 합니다. 아키텍처의 품질을 평가하기 위한 것은 아닙니다. 솔루션에서 Microsoft 서비스를 사용하는 방법을 보여 주도록 설계되었습니다.

참조 아키텍처 다이어그램은 여러 도구를 통해 만들 수 있습니다. Azure 아키텍처 모델을 나타내는 여러 스텐실이 있으므로 Microsoft Visio를 권장합니다.

참조 아키텍처 다이어그램을 작성하는 데 도움이 되는 시작점은 [Azure 아키텍처 모델](/azure/architecture/browse/)을 활용하는 것입니다.

## <a name="typical-components-of-a-reference-architecture-diagram"></a>참조 아키텍처 다이어그램의 일반적인 구성 요소

다이어그램은 Microsoft Azure를 배포 하 고 소비 하는 솔루션, 응용 프로그램 또는 서비스 코드로 IP를 명확 하 게 식별 해야 합니다. 이 코드는 항상 재사용 가능 해야 하며 배포 당 광범위 한 사용자 지정에 의존 하지 않아야 합니다.

- Azure 리소스를 사용하는 클라우드 서비스를 포함하여 제품을 호스트하고 상호 작용하는 클라우드 서비스
- 제품에서 사용하는 데이터 연결, 데이터 계층 및 데이터 서비스
- 제품의 보안, 인증 및 사용자를 제어하는 데 사용되는 클라우드 서비스
- 사용자에게 제품을 제공하는 사용자 인터페이스 및 기타 서비스
- 하이브리드 또는 온-프레미스 연결 및 통합 또는 둘 다의 조합

이 스크린샷은 참조 아키텍처 다이어그램의 예제를 보여 줍니다.

[![이 이미지는 공동 판매 아키텍처 다이어그램의 예제입니다.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

이 예제 참조 아키텍처 다이어그램은 기계 학습 알고리즘을 통해 수요 시나리오를 예측하는 데 도움이 되도록 인트라넷 사이트와 통합될 수 있는 전문 업계 챗봇용입니다. 이 솔루션은 서로 다른 ERP 시스템의 공급망 및 제조 일정 데이터를 사용합니다. 봇은 판매 직원이 주문에 대해 가능한 배달 날짜를 커밋할 수 있는 시기에 대한 질문을 해결하도록 설계되었습니다.

## <a name="next-steps"></a>다음 단계

- [상업용 마켓플레이스 제품에 대한 공동 판매 구성](./co-sell-configure.md)
