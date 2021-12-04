---
title: 제품 유형별 게시 가이드 - Microsoft 상업용 Marketplace
description: 이 문서에서는 Microsoft 상업용 Marketplace(Azure Marketplace)에서 사용할 수 있는 제품 유형을 설명합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 12/03/2021
ms.openlocfilehash: ead823559f2f14253c1a708d97c76df27bb7b1e8
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133545087"
---
# <a name="publishing-guide-by-offer-type"></a>제품 형식별 게시 가이드

이 문서에서는 상업용 Marketplace에서 사용할 수 있는 제품 유형을 설명합니다. 상업용 Marketplace에 제공된 메타데이터, 아티팩트 및 기타 콘텐츠를 포함한 제품 구조는 *제품 유형* 에 의해 정의됩니다.

[게시 옵션을 결정](determine-your-listing-type.md)한 후 파트너 센터에서 제품 작성을 시작하기 전에 제품 유형을 선택해야 합니다. 제품 유형은 게시하려는 솔루션, 앱 또는 서비스 제품의 유형뿐만 아니라 Microsoft 제품 및 서비스에 대한 맞춤도 해당됩니다.

> [!NOTE]
> 제품 유형을 선택한 후에는 제품을 다른 유형으로 변경할 수 없습니다. 다른 제품 유형을 만들려면 새 제품을 만들어야 합니다.

여러 가지 방법으로 단일 제품 유형을 구성하여 다양한 게시 옵션, 나열 옵션, 프로비저닝 또는 가격 책정을 사용할 수 있습니다. 제품 유형의 게시 옵션과 구성은 제품 자격 및 기술 요구 사항에도 맞춥니다.

제품을 만들기 전에 온라인 스토어 및 제품 유형 자격 요구 사항 및 기술 게시 요구 사항을 검토해야 합니다.

## <a name="list-of-offer-types"></a>제품 유형 목록

다음 표에서는 파트너 센터의 상업용 Marketplace 제품 유형을 보여 줍니다.

| **제품 유형**    | **설명**  |
| :------------------- | :-------------------|
| [**Azure 애플리케이션**](plan-azure-application-offer.md) | Azure 애플리케이션 계획에는 _솔루션 템플릿_ 과 _관리형 애플리케이션_ 의 두 가지 종류가 있습니다. 두 플랜 유형 모두 단일 VM(가상 머신) 이외의 솔루션 배포와 구성을 자동화하는 기능을 지원합니다. VM, 네트워킹, 스토리지 리소스를 비롯한 여러 리소스를 제공하여 IaaS 솔루션과 같은 복잡한 솔루션을 제공하는 프로세스를 자동화할 수 있습니다. 두 플랜 유형 모두 VM에 대한 제한 없이 여러 종류의 Azure 리소스를 사용할 수 있습니다.<ul><li>**솔루션 템플릿** 플랜은 상업용 Marketplace에 솔루션을 게시하는 주요 방법 중 하나입니다. 솔루션 템플릿 플랜은 상업용 Marketplace에서 거래할 수 없지만 상업용 Marketplace를 통해 청구되는 유료 VM 제품을 배포하는 데 사용할 수 있습니다. 고객이 솔루션을 관리하고 트랜잭션이 다른 계획을 통해 청구되는 경우 솔루션 템플릿 계획 유형을 사용합니다.</li><br><li>**관리형 애플리케이션** 계획을 사용하면 고객을 위한 완전 관리형 턴키 애플리케이션을 쉽게 구축하고 제공할 수 있습니다. 몇 가지 주요 차이점을 포함하여 솔루션 템플릿 플랜과 동일한 기능을 제공합니다.</li><ul><li> 리소스는 리소스 그룹에 배포되고 앱 게시자가 관리합니다. 리소스 그룹은 소비자의 구독에 있지만, 게시자 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다.</li><li>게시자는 솔루션의 지속적인 지원 비용을 지정하고 상업용 Marketplace를 통해 트랜잭션을 지원합니다.</li></ul>사용자 또는 고객이 솔루션을 파트너에서 관리하거나 구독 기반 솔루션을 배포해야 하는 경우 관리형 애플리케이션 계획 유형을 사용합니다.</ul> |
| [**Azure Container**](marketplace-containers.md) | 솔루션이 Kubernetes 기반 Azure Container Service로 프로비저닝된 Docker 컨테이너 이미지인 경우 Azure Container 제품 유형을 사용합니다. |
| [**Azure Virtual Machine**](marketplace-virtual-machines.md) | 고객과 연결된 구독에 가상 어플라이언스를 배포할 경우 가상 머신 제품 형식을 사용합니다. |
| [**컨설팅 서비스**](./plan-consulting-service-offer.md) | 컨설팅 서비스는 고객을 서비스와 연결하여 Azure, Dynamics 365 또는 Power Suite 서비스의 사용을 지원하고 확장하는 데 도움이 됩니다.|
| [**Dynamics 365**](marketplace-dynamics-365.md) | Dynamics 365 제품을 기반으로 하거나 확장하는 AppSource 제품을 게시합니다.|
| [**IoT Edge 모듈**](marketplace-iot-edge.md) | Azure IoT Edge 모듈은 IoT Edge가 관리하는 최소 계산 단위이며 Microsoft 서비스(예: Azure Stream Analytics), 타사 서비스 또는 고유한 솔루션별 코드를 포함할 수 있습니다. |
| [**관리되는 서비스**](./plan-managed-service-offer.md) | [Azure Lighthouse](../lighthouse/overview.md)를 통해 관리되는 서비스 제품을 만들고 고객이 위임한 구독 또는 리소스 그룹을 관리합니다.|
| [**Power BI 앱** <br/> **Microsoft 365**](marketplace-dynamics-365.md) | Power BI 및 Microsoft 365를 빌드하거나 확장하는 AppSource 제품을 게시합니다.|
| [**SaaS(Software as a Service)**](plan-saas-offer.md) | SaaS(software as a service) 제품 유형을 사용하면 고객이 SaaS 기반, 기술 솔루션을 구독으로 구매할 수 있습니다. SaaS 제품에 대한 Single Sign-On 요구 사항에 대한 자세한 내용은 [상업용 Marketplace의 Azure AD 및 거래 가능한 SaaS 제품](azure-ad-saas.md)을 참조하세요. |

> [!IMPORTANT]
> **SaaS 제품 및 and Microsoft 365 추가 기능**: 마켓플레이스 고객이 제품을 보고 구매할 수 있는 방법에 트랜잭션 기능이 끼치는 영향에 대한 자세한 내용은 [상업용 Marketplace에서의 거래](marketplace-commercial-transaction-capabilities-and-considerations.md)를 참조하세요. SaaS 제품의 경우 제품의 트랜잭션 기능과 범주 선택에 따라 제품이 게시될 온라인 상점이 결정됩니다.

## <a name="next-steps"></a>다음 단계

- 제품 선택 및 구성을 완료하려면 제품 유형에 대한 해당 문서의 자격 요구 사항을 검토합니다.
- 솔루션이 제품 유형 및 구성에 매핑되는 방법에 대한 예는 각 온라인 스토어별 게시 패턴을 검토하세요.
