---
title: Azure Key Vault 관리형 HSM - 타사 솔루션 | Microsoft Docs
description: 관리형 HSM과 통합된 타사 솔루션에 관해 알아봅니다.
services: key-vault
author: mbaldwin
editor: ''
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: mbaldwin
ms.openlocfilehash: e70afc4a89fc0c9ce5c6ec59cd795b83b5e078b0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443472"
---
# <a name="third-party-solutions"></a>타사 솔루션

Azure Key Vault 관리형 HSM은 **FIPS 140-2 수준 3** 유효성이 검사된 HSM을 사용하여 클라우드 애플리케이션용 암호화 키를 보호할 수 있는 완전 관리형 고가용 단일 테넌트 표준 규격 클라우드 서비스입니다. [자세히 알아보기](overview.md).

여러 공급업체가 Microsoft와 긴밀하게 협력하여 관리형 HSM과 자사 솔루션을 통합했습니다. 아래 표에는 솔루션과 간략한 설명(공급업체에서 제공)이 나와 있습니다. Azure Marketplace 제공 및 설명서 링크도 제공됩니다.


## <a name="third-party-solutions-integrated-with-managed-hsm"></a>관리형 HSM과 통합된 타사 솔루션

| 공급업체 이름 | 솔루션 설명 |
|-------------|-------------------------------------------------|
|[Cloudflare](https://cloudflare.com)|Cloudflare의 키 없는 SSL을 사용하면 웹 사이트가 관리형 HSM에서 프라이빗 키를 계속 관리하면서 Cloudflare의 SSL 서비스를 이용할 수 있습니다. 관리형 HSM과 결합된 이 서비스는 프라이빗 키를 보호하고, 서명 및 암호화 작업을 내부적으로 수행하고, 액세스 제어를 제공하고, 변조 방지 FIPS 140-2 수준 3 HSM에 키를 저장하여 높은 수준의 보호 기능을 제공합니다. <br>[문서](https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/azure-managed-hsm)
|[NewNet Communication Technologies](https://newnet.com/)|NewNet의 STC(Secure Transaction Cloud)는 클라우드 기반의 가상화된 HSM으로 보강된 업계 최초의 클라우드 기반 보안 결제 라우팅, 전환, 전송 솔루션으로, 모바일, 웹, 매장 내 결제를 처리합니다. STC를 사용하면 결제 엔터티를 위한 클라우드 변환과 새로운 결제 공급자를 위한 신속한 배포가 가능합니다.<br/>[Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/newnetcommunicationtechnologies1589991852134.secure_transaction_cloud?tab=overview)<br/>[문서](https://newnet.com/business-units/secure-transactions/products/secure-transaction-cloud-stc/)|
|[PrimeKey](https://www.primekey.com)|세계에서 가장 많이 사용되는 PKI(퍼블릭 키 인프라)인 EJBCA Enterprise는 신뢰할 수 있는 ID에 대한 기본 보안 서비스와 모든 사용 사례에 대한 보안 통신을 제공합니다. EJBCA Enterprise의 단일 인스턴스에서 여러 CA와 수준을 지원하므로 여러 사용 사례를 위한 완전한 인프라를 빌드할 수 있습니다.<br>[Azure Marketplace 제품](https://azuremarketplace.microsoft.com/marketplace/apps/primekey.ejbca_enterprise_cloud_2)<br/>[문서](https://doc.primekey.com/x/a4z_/)|



## <a name="next-steps"></a>다음 단계
* [관리형 HSM 개요](overview.md)
* [관리형 HSM 모범 사례](best-practices.md)

