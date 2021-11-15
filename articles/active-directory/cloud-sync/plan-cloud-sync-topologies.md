---
title: Azure AD Connect 클라우드 동기화에서 지원하는 토폴로지 및 시나리오
description: 이 문서에서는 Azure AD Connect 클라우드 동기화를 사용하는 다양한 온-프레미스 및 Azure AD(Azure Active Directory) 토폴로지에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70a480fba6b7923ffb327b22822e4baa3a54acdc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749960"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Azure AD Connect 클라우드 동기화에서 지원하는 토폴로지 및 시나리오
이 문서에서는 Azure AD Connect 클라우드 동기화를 사용하는 다양한 온-프레미스 및 Azure AD(Azure Active Directory) 토폴로지에 대해 설명합니다. 이 문서에는 지원되는 구성 및 시나리오만 포함되어 있습니다.

> [!IMPORTANT]
> Microsoft는 공식적으로 문서화된 구성 또는 작업 외의 Azure AD Connect 클라우드 동기화에 대한 수정 또는 작업을 지원하지 않습니다. 해당 구성 또는 작업으로 인해 Azure AD Connect 클라우드 동기화의 불일치 또는 지원되지 않는 상태가 발생할 수 있으며, 그 결과 Microsoft가 해당 배포에 대해 기술 지원을 제공할 수 없습니다.

자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWJ8l5]

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>모든 시나리오 및 토폴로지에 대해 기억할 사항
다음은 솔루션을 선택할 때 염두에 두어야 하는 정보 목록입니다.

- 사용자 및 그룹은 모든 포리스트에서 고유하게 식별해야 함
- 클라우드 동기화에서는 포리스트 간 일치가 발생하지 않음
- 사용자 또는 그룹은 모든 포리스트에서 한 번만 표시되어야 함
- 개체의 원본 앵커는 자동으로 선택되며  ms-DS-ConsistencyGuid를 사용합니다(있는 경우). 이 GUID가 없으면 ObjectGUID를 사용합니다.
- 원본 앵커에 사용되는 특성은 변경할 수 없습니다.

## <a name="single-forest-single-azure-ad-tenant"></a>단일 포리스트, 단일 Azure AD 테넌트
![단일 포리스트와 단일 테넌트에 대한 토폴로지를 보여 주는 다이어그램](media/tutorial-single-forest/diagram-2.png)

가장 간단한 토폴로지는 하나 이상의 도메인 및 단일 Azure AD 테넌트를 포함하는 단일 온-프레미스 포리스트입니다.  이 시나리오의 예제는 [자습서: 단일 Azure AD 테넌트를 포함하는 단일 포리스트](tutorial-single-forest.md)를 참조하세요.


## <a name="multi-forest-single-azure-ad-tenant"></a>다중 포리스트, 단일 Azure AD 테넌트
![다중 포리스트 및 단일 테넌트에 대한 토폴로지](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

일반적인 토폴로지는 하나 이상의 도메인 및 단일 Azure AD 테넌트를 포함하는 다중 AD 포리스트입니다.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Azure AD Connect를 사용하는 기존 포리스트, 클라우드 프로비저닝을 사용하는 새 포리스트
![기존 포리스트와 새 포리스트에 대한 토폴로지를 보여 주는 다이어그램](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

이 시나리오에서는 토폴로지가 다중 포리스트 시나리오와 유사하지만, 기존 Azure AD Connect 환경을 포함하고 Azure AD Connect 클라우드 동기화를 사용하여 새 포리스트를 가져옵니다. 이 시나리오의 예제는 [자습서: 단일 Azure AD 테넌트를 포함하는 기존 포리스트](tutorial-existing-forest.md)를 참조하세요.

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>기존 하이브리드 AD 포리스트에서 Azure AD Connect 클라우드 동기화 파일럿
![단일 포리스트 및 단일 테넌트에 대한 토폴로지](media/tutorial-migrate-aadc-aadccp/diagram-2.png) 파일럿 시나리오에는 동일한 포리스트에 Azure AD Connect 및 Azure AD Connect 클라우드 동기화를 모두 포함하고 그에 따라 사용자 및 그룹의 범위를 지정합니다. 참고: 개체는 도구 중 하나에서만 범위 내에 있어야 합니다. 

이 시나리오의 예제는 [자습서: 기존 동기화된 AD 포리스트에서 Azure AD Connect 클라우드 동기화 파일럿](tutorial-pilot-aadc-aadccp.md)을 참조하세요.



## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)

