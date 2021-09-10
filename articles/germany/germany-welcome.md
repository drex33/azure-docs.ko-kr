---
title: Azure 독일 개요 | Microsoft Docs
description: 이 문서에서는 Azure 독일 클라우드 기능 및 독일어 데이터 개인 정보 보호 규정에 대한 규정 준수 요구 사항을 지원하는 신뢰할 수 있는 디자인 및 보안에 대해 간단히 설명합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 394e804faa0d6f6036a025cb4cad0119af5c3837
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122566173"
---
# <a name="welcome-to-azure-germany"></a>Azure 독일 시작

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="overview"></a>개요
Microsoft Azure 독일에서는 [보안, 개인 정보 보호, 규정 준수 및 투명도라는 기본 원칙](https://azure.microsoft.com/overview/clouds/germany/)을 기준으로 구축된 클라우드 플랫폼을 제공합니다. Azure 독일은 물리적으로 분리된 Microsoft Azure 인스턴스입니다. 여기서는 해당 아키텍처를 기준으로 하는 모든 시스템 및 애플리케이션에 대한 독일 데이터 개인 정보 보호 규정에 중요한 세계 최고 수준의 보안 및 [규정 준수 서비스](https://azure.microsoft.com/support/trust-center/compliance/)를 사용합니다. 데이터 트러스티가 운영하는 Azure 독일은 온-프레미스 또는 클라우드에서 솔루션을 빌드 및 배포하는 여러 하이브리드 시나리오를 지원합니다. 인스턴트 확장성을 활용하여 하이퍼스케일 클라우드 서비스가 작동하도록 할 수 있습니다.

Azure 독일에서는 전송 중 및 미사용 상태의 데이터가 보존되며, 비즈니스 연속성을 위해 독일 데이터 센터 간에 데이터를 복제합니다. 두 데이터 센터의 고객 데이터는 데이터 트러스티인 T-Systems International의 제어 하에 관리됩니다. 이 트러스티는 독립적인 독일 회사이며 Deutsche Telekom의 자회사입니다. 고객 또는 데이터 트러스티의 허가가 있을 때만 액세스 권한이 제공되므로 고객의 데이터가 추가적으로 제어될 수 있습니다.

이러한 데이터 센터에서 Microsoft의 상용 클라우드 서비스는 독일 데이터 처리 규정을 준수하고 고객에게 데이터를 처리하는 방법과 위치에 대한 추가 선택 항목을 제공합니다.

Azure 독일에는 IaaS(Infrastructure-as-a-Service), PaaS(Platform-as-a-Service) 및 SaaS(Software-as-a-Service)의 핵심 구성 요소가 포함되어 있습니다. 해당 구성 요소에는 인프라, 네트워크, 스토리지, 데이터 관리, ID 관리 및 기타 서비스가 포함됩니다.

Azure 독일은 지리 동기식 데이터 복제 및 자동 스케일링과 같이 글로벌 Azure 고객이 사용한 것과 동일한 유용한 기능을 대부분 지원합니다. 

## <a name="azure-germany-documentation"></a>Azure 독일 설명서
이 사이트는 [Microsoft Azure 독일](https://azure.microsoft.com/overview/clouds/germany/) 서비스의 기능을 설명하고 모든 고객에게 적용되는 일반적인 지침을 제공합니다. Azure 독일 구독에 특별히 규제된 데이터를 포함하기 전에 Azure 독일 기능을 숙지해야 합니다.

특정 승인 및 규정이 적용되는 Azure 독일 서비스에 대한 최신 정보는 [Microsoft Azure 보안 센터 규정 준수 페이지](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)를 참조하세요. 추가 Microsoft 서비스를 사용할 수도 있지만 Azure 독일 대상 서비스 또는 이 설명서의 범위에 포함되지 않습니다. Azure 독일 서비스는 별도 약관 및 개인 정보 보호 정책 아래에서 타사 또는 Microsoft에 의해 제공되는 다양한 추가 리소스, 애플리케이션 또는 서비스를 사용하도록 허용할 수도 있습니다. 이러한 내용은 이 설명서의 범위에도 해당되지 않습니다. 규정 준수에 대한 사용자의 요구를 충족하는지 확인하기 위해 Azure Marketplace 제품과 같은 이러한 모든 "추가 기능" 제품의 용어를 검토할 책임이 있습니다.

Azure 독일은 영국을 비롯한 EU/EFTA에서 비즈니스를 수행하려는 적격 고객 및 파트너에게 글로벌로 제공됩니다.

## <a name="general-guidance-for-customers"></a>고객을 위한 일반 지침
현재 제공되는 대부분의 기술 콘텐츠는 Azure 독일이 아닌 글로벌 Azure를 위해 개발되고 있다고 가정하고 있습니다. 개발자가 Azure 독일에서 호스트되도록 개발된 애플리케이션의 주요 차이점을 알고 있도록 하는 것이 중요합니다.

* 글로벌 Azure의 특정 지역에 있는 특정 서비스와 기능이 Azure 독일에서 제공되지 않을 수 있습니다. 일반적으로 사용 가능한 가장 최근의 서비스에 대해서는 [지역 페이지](https://azure.microsoft.com/regions/services)를 참조하세요. 
* Azure 독일에서 제공되는 기능의 경우 글로벌 Azure와 다르게 구성되어 있습니다. 샘플 코드, 구성 및 단계를 검토하여 Azure 독일 환경 내에서 빌드하고 실행하는지 확인해야 합니다.
* Azure 독일 경계를 식별하는 정보와 고객 규제/제어 데이터 지침 및 모범 사례에 대해서는 이 사이트의 Azure 독일 기술 서비스 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트를 보려면 [Azure 독일 블로그](/archive/blogs/azuregermany/)를 구독하세요.

Azure 독일에 대해 알아보려면 다음 링크를 참조하세요.

* [평가판에 등록](https://azure.microsoft.com/free/germany/)
* Azure 독일 계정이 이미 있는 경우 [로그인](https://portal.microsoftazure.de/)
* [Azure 독일 구입 및 액세스](https://azure.microsoft.com/overview/clouds/germany/)