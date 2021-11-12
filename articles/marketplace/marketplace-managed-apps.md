---
title: Azure 애플리케이션 관리형 애플리케이션 제품 게시 가이드 - Azure Marketplace
description: 이 문서에서는 관리형 애플리케이션을 Azure Marketplace에 게시하기 위한 요구 사항을 설명합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 11/11/2021
ms.openlocfilehash: 3a0ebed6767666373095b9ec33d2f20228c9d373
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289664"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Azure 관리형 애플리케이션 게시 가이드

Azure *관리형 애플리케이션* 은 Azure Marketplace에 Azure 애플리케이션을 게시하는 한 가지 방법입니다. 관리형 애플리케이션은 Azure Marketplace를 통해 배포되고 청구되는 트랜잭션 제품입니다. 사용자에게 표시되는 목록 옵션은 *지금 가져오기* 입니다.

이 문서에서는 관리형 애플리케이션 제품 유형에 대한 요구 사항을 설명합니다.

다음 조건에서 관리형 애플리케이션 제품 유형을 사용합니다.

- VM(가상 머신) 또는 전체 IaaS(Infrastructure as a Service) 기반 솔루션을 사용하여 고객을 위한 구독 기반 솔루션을 배포하려고 합니다.
- 본인 또는 고객은 솔루션을 파트너가 관리하도록 요구합니다.

>[!NOTE]
>예를 들어, 파트너는 시스템 통합자 또는 MSP(관리되는 서비스 공급자)일 수 있습니다.  

## <a name="managed-application-offer-requirements"></a>관리되는 응용 프로그램 제품 요구 사항

|요구 사항 |세부 정보  |
|---------|---------|
|Azure 구독 | 관리되는 응용 프로그램은 고객의 구독에 배포되어야 하지만 타사에서 관리할 수 있습니다. |
|청구 및 계량    |  리소스는 고객의 Azure 구독에서 제공됩니다. 종량제 지불 모델을 사용하는 Azure 리소스는 Microsoft를 통해 고객과 거래되며 고객의 Azure 구독을 통해 청구됩니다. <br><br> BYOL Azure 리소스의 경우 Microsoft는 고객 구독에 청구된 모든 인프라 비용을 청구하지만 사용자는 고객에게 직접 소프트웨어 라이선스 요금을 청구합니다.        |
|Azure 관리형 애플리케이션 패키지    |   고객의 구독에 애플리케이션을 배포하는 데 사용되는 구성된 Azure Resource Manager 템플릿 및 UI 정의 만들기입니다.<br><br>관리형 애플리케이션을 만드는 방법에 대한 자세한 내용은 [관리형 애플리케이션 개요](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)를 참조하세요.|

---

> [!NOTE]
> 관리형 애플리케이션은 Azure Marketplace를 통해 배포 가능해야 합니다. 고객 커뮤니케이션이 중요한 경우, 잠재 고객 공유를 사용하도록 설정한 후 관심 있는 고객에게 연결해야 합니다.  

> [!Note]
> 이제 CSP(클라우드 솔루션 공급자) 파트너 채널 옵트인을 사용할 수 있습니다. Microsoft CSP 파트너 채널을 통한 제품 마케팅에 대한 자세한 내용은 [클라우드 솔루션 공급자](./cloud-solution-providers.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않은 경우 [Azure Marketplace로 클라우드 비즈니스를 확장](https://azuremarketplace.microsoft.com/sell)하는 방법을 알아보세요.

파트너 센터에서 등록하고 작업을 시작하려면

- 제품을 만들거나 완성할 수 있도록 [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)합니다.
- 자세한 내용은 [Azure 애플리케이션 제품 만들기](azure-app-offer-setup.md)를 참조하세요.
