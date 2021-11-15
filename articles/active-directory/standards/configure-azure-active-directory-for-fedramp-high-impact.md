---
title: FedRAMP High 영향 수준을 충족하도록 Azure Active Directory 구성
description: Azure Active Directory를 사용하여 조직을 위해 FedRAMP High 영향 수준을 충족하는 방법에 대한 개요입니다.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20eafed03b9dd63bf6c59445d744d7a3c67c5614
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601751"
---
# <a name="configure-azure-active-directory-to-meet-fedramp-high-impact-level"></a>FedRAMP High 영향 수준을 충족하도록 Azure Active Directory 구성

FedRAMP([Federal Risk and Authorization Management Program](https://www.fedramp.gov/))은 CSP(클라우드 서비스 공급자)에 대한 평가 및 권한 부여 프로세스입니다. 특히 프로세스는 연방 기관에서 사용할 CSO(클라우드 솔루션 제품)를 만드는 CSP를 위한 것입니다. Azure와 Azure Government는 FedRAMP 인증의 가장 높은 기준인 Joint Authorization Board의 [High Impact 수준 P-ATO(Provisional Authority to Operate)](/compliance/regulatory/offering-fedramp)를 획득했습니다.

Azure는 CSO 또는 연방 기관을 위해 FedRAMP High 등급을 받도록 제어 요구 사항을 모두 충족하는 기능을 제공합니다. 규정을 준수하기 위해 추가적인 구성 또는 프로세스를 완료하는 것은 조직의 책임입니다. 이러한 책임은 CSO를 위해 FedRAMP High 인증을 받고자 하는 CSP와 ATO(Authority to Operate)를 필요로 하는 연방 기관 모두에 적용됩니다. 

## <a name="microsoft-and-fedramp"></a>Microsoft 및 FedRAMP 

Microsoft Azure는 다른 CSP에 비해 더욱 다양한 [FedRAMP High 영향](../../azure-government/compliance/azure-services-in-fedramp-auditscope.md) 수준의 서비스를 지원합니다. 그리고 이러한 수준의 Azure 퍼블릭 클라우드는 미국 정부 고객의 요구를 충족하지만 요구 사항이 이보다 엄격한 기관은 Azure Government 클라우드를 활용할 수 있습니다. Azure Government는 직원 심사 강화와 같은 추가적인 보호 기능을 제공합니다. 

Microsoft는 이러한 인증을 유지하기 위해 매년 클라우드 서비스에 대한 재인증을 받아야 합니다. 이를 위해 Microsoft는 보안 제어를 지속적으로 모니터링 및 평가하고 서비스의 보안이 규정 준수 상태로 유지됨을 입증합니다. 자세한 내용은 [Microsoft 클라우드 서비스 FedRAMP 인증](https://marketplace.fedramp.gov/) 및 [Microsoft FedRAMP 감사 보고서](https://aka.ms/MicrosoftFedRAMPAuditDocuments)를 참조하세요. 기타 FedRAMP 보고서를 받아 보시려면 [Azure 연방 문서](mailto:AzFedDoc@microsoft.com)로 메일을 보내 문의하세요.

FedRAMP 인증을 받는 방법은 다양합니다. 여기에서 Azure의 기존 인증 패키지와 참고 자료를 다시 사용하면 ATO 또는 P-ATO를 확보하는 데 필요한 시간과 노력을 크게 줄일 수 있습니다. 

## <a name="scope-of-guidance"></a>지침의 범위

FedRAMP High 기준은 [NIST 800-53 Security Controls Catalog Revision 4](https://csrc.nist.gov/publications/detail/sp/800-53/rev-4/final)의 421개 컨트롤 및 컨트롤 개선 사항으로 구성됩니다. 해당하는 경우 [800-53 Revision 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final)의 상세한 정보를 포함했습니다. 본 문서에서는 ID와 관련된 이러한 컨트롤 일부와 구성해야 하는 컨트롤을 설명합니다. 

Azure Active Directory(Azure AD)에서 구성하는 데 사용하는 컨트롤을 준수하는 데 도움이 되는 규범적인 지침을 제공합니다. 일부 ID 제어 요구 사항을 완전히 충족하려면 다른 시스템을 사용해야 할 수 있습니다. 다른 시스템에는 Azure Sentinel과 같은 보안 정보 및 이벤트 관리 도구가 포함되어 있을 수 있습니다. Azure Active Directory 외부에서 Azure 서비스를 사용하는 경우 고려해야 하는 다른 컨트롤이 있으며 Azure에서 이미 제공되고 있는 기능을 사용하여 컨트롤을 충족할 수 있습니다.

FedRAMP 리소스 목록은 다음과 같습니다.

* [Federal Risk and Authorization Management Program](https://www.fedramp.gov/)

* [FedRAMP 보안 평가 프레임워크](https://www.fedramp.gov/assets/resources/documents/FedRAMP_Security_Assessment_Framework.pdf)

* [FedRAMP 인증에 대한 기관 가이드](https://www.fedramp.gov/assets/resources/documents/Agency_Guide_for_Reuse_of_FedRAMP_Authorizations.pdf)

* [Microsoft의 클라우드 규정 준수 관리](https://www.microsoft.com/trustcenter/common-controls-hub)

* [Microsoft Government Cloud](https://go.microsoft.com/fwlink/p/?linkid=2087246)

* [Azure 규정 준수 제품](https://aka.ms/azurecompliance)

* [FedRAMP High Azure Policy 기본 제공 이니셔티브 정의](../../governance/policy/samples/fedramp-high.md)

* [Microsoft 365 규정 준수 센터](/microsoft-365/compliance/microsoft-365-compliance-center)

* [Microsoft Compliance Manager](/microsoft-365/compliance/compliance-manager)

## <a name="next-steps"></a>다음 단계

[액세스 제어 구성](fedramp-access-controls.md)

[식별 및 인증 컨트롤 구성](fedramp-identification-and-authentication-controls.md)

[기타 컨트롤 구성](fedramp-other-controls.md)

