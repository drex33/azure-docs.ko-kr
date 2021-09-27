---
title: 청사진 샘플 인덱스
description: Azure 청사진을 사용하여 환경, 정책 및 Cloud Adoptions Framework 기반을 배포하기 위한 규정 준수 및 표준 샘플의 인덱스입니다.
ms.date: 08/17/2021
ms.topic: sample
ms.openlocfilehash: 31cf137577301402cd9e26d96a1204f427331daf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658301"
---
# <a name="azure-blueprints-samples"></a>Azure Blueprints 샘플

다음 표에는 Azure Blueprints의 샘플에 대한 링크가 포함되어 있습니다. 각 샘플은 프로덕션 품질이며 지금 바로 배포하여 다양한 규정 준수 요구 사항을 충족할 수 있습니다.

## <a name="standards-based-blueprint-samples"></a>표준 기반 Blueprint 샘플

| 샘플 | Description |
|---------|---------|
| [오스트레일리아 정부 ISM PROTECTED](./ism-protected/index.md) | 오스트레일리아 정부 ISM PROTECTED 규정 준수에 대한 보호책을 제공합니다. |
| [Azure Security Benchmark Foundation](./azure-security-benchmark-foundation/index.md) | Azure Security Benchmark Foundation을 배포하고 구성합니다. |
| [캐나다 연방 PBMM](./canada-federal-pbmm.md) | Canada Federal Protected B, 중간 무결성, 중간 가용성(PBMM) 규정 준수를 위한 가드레일을 제공합니다. |
| [CIS Microsoft Azure Foundations Benchmark v1.3.0](./cis-azure-1-3-0.md) | CIS Microsoft Azure Foundations Benchmark v1.3.0 권장 사항을 준수하는 데 도움이 되는 일련의 정책을 제공합니다. |
| [CIS Microsoft Azure Foundations Benchmark v1.1.0](./cis-azure-1-1-0.md) | CIS Microsoft Azure Foundations Benchmark v1.1.0 권장 사항을 준수하는 데 도움이 되는 일련의 정책을 제공합니다. |
| [CMMC Level 3](./cmmc-l3.md) | CMMC 수준 3 규정 준수를 위한 가드 레일을 제공합니다. |
| [HIPAA HITRUST 9.2](./hipaa-hitrust-9-2.md) | HIPAA HITRUST를 준수하는 데 도움이 되는 정책 세트를 제공합니다. |
| [IRS 1075 2016년 9월](./irs-1075-sept2016.md) | IRS 1075 규정 준수를 위한 가드레일을 제공합니다.|
| [ISO 27001](./iso-27001-2013.md) | ISO 27001 규정 준수를 위한 가드레일을 제공합니다. |
| [ISO 27001 Shared Services](./iso27001-shared/index.md) | ISO 27001 증명에 도움이 되는 일련의 호환 인프라 패턴 및 정책 가드 레일을 제공합니다. |
| [ISO 27001 App Service Environment/SQL Database 워크로드](./iso27001-ase-sql-workload/index.md) | [ISO 27001 Shared Services](./iso27001-shared/index.md) 청사진 샘플에 더 많은 인프라를 제공합니다. |
| [미디어](./media/index.md) | Media MPAA를 준수하는 데 도움이 되는 정책 세트를 제공합니다. |
| [뉴질랜드 ISM 제한됨](./new-zealand-ism.md) | 특정 뉴질랜드 정보 보안 수동 컨트롤을 해결하는 정책을 할당합니다. |
| [NIST SP 800-171 R2](./nist-sp-800-171-r2.md) | NIST SP 800-171 R2 규정 준수를 위한 가드레일을 제공합니다. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | PCI-SS v3.2.1 규정 준수를 위한 일련의 정책을 제공합니다. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | SWIFT CSP-CSCF v2020 규정 준수를 지원합니다. |
| [영국 공식 및 영국 NHS 거버넌스](./ukofficial-uknhs.md) | 영국 공식 및 영국 NHS 증명에 도움이 되는 일련의 호환 인프라 패턴 및 정책 가드 레일을 제공합니다. |
| [CAF Foundation](./caf-foundation/index.md) | [Azure에 대한 Microsoft CAF(클라우드 채택 프레임워크)](/azure/architecture/cloud-adoption/governance/journeys/index)와 연계하여 클라우드 자산을 관리하는 데 도움이 되는 컨트롤 세트를 제공합니다. |
| [CAF Migrate 방문 영역](./caf-migrate-landing-zone/index.md) | 첫 번째 워크로드 마이그레이션을 설정하고 [Azure에 대한 Microsoft CAF(클라우드 채택 프레임워크)](/azure/architecture/cloud-adoption/migrate/index)와 연계하여 클라우드 자산을 관리하는 데 도움이 되는 컨트롤 세트를 제공합니다. |

## <a name="samples-strategy"></a>샘플 전략

:::image type="complex" source="../media/blueprint-samples-strategy.png" alt-text="아키텍처 복잡성과 규정 준수 요구 사항에 적합한 위치를 보여주는 다이어그램입니다." border="false":::
   X축이 아키텍처 복잡성, Y축이 규정 준수 요구 사항인 좌표계를 설명합니다. 아키텍처 복잡성과 규정 준수 요구 사항이 증가함에 따라 지역 E에 지정된 포털에서 표준 청사진 샘플을 채택합니다. Azure를 시작하는 고객의 경우 지역 A 및 B로 지정된 C A F(클라우드 채택 프레임워크) 기반 파운데이션 및 랜딩 존 청사진을 사용합니다. 나머지 공간은 고객이 C, D 및 F 지역의 파트너로 만든 사용자 지정 청사진에 의한 것입니다. :::image-end:::

CAF Foundation 및 CAF Migrate 방문 영역 청사진은 고객이 온-프레미스 자산과 워크로드를 Azure로 마이그레이션하기 위해 기존의 완전한 단일 구독을 준비하는 것으로 가정합니다.
(그림의 지역 A 및 B).

샘플 청사진을 반복하고 고객이 적용하는 사용자 지정의 패턴을 찾을 수 있습니다. 또한 금융 서비스 및 전자 상거래(지역 B의 위쪽 끝)와 같이 업계와 관련된 청사진을 사전에 해결할 수 있습니다. 마찬가지로, 여러 구독, 고가용성, 지역 간 리소스, 그리고 기존 구독 및 리소스(지역 C 및 D)에 대한 제어를 구현하는 고객과 같이 복잡한 아키텍처 고려 사항에 대한 청사진 작성을 계획하고 있습니다.

규정 준수 요구사항이 높고 아키텍처 복잡성이 높은 고객 시나리오를 다루는 샘플 청사진이 있습니다(그림의 지역 E). 그림의 지역 F는 샘플 청사진을 적용하여 고객 및 파트너의 고유한 요구에 맞게 맞춤화합니다.

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결
