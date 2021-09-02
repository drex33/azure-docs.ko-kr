---
title: Azure AD에서 암호 해시 동기화란? | Microsoft Docs
description: 암호 해시 동기화를 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4fb6c1dbeca8c2073a0a493e67b05d46d36738
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223224"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Azure AD에서 암호 해시 동기화란?
암호 해시 동기화는 하이브리드 ID를 설정하기 위해 사용된 로그인 메서드 중 하나입니다. Azure AD Connect는 사용자 암호 해시의 해시를 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure AD 인스턴스로 동기화합니다.

암호 해시 동기화는 Azure AD Connect 동기화를 통한 디렉터리 동기화 기능 구현의 확장판입니다. 이 기능을 사용하여 Microsoft 365와 같은 Azure AD 서비스에 로그인할 수 있습니다. 온-프레미스 Active Directory 인스턴스에 로그인하기 위해 사용하는 암호와 동일한 암호를 사용하여 서비스에 로그인합니다.

![Azure AD Connect의 정의](./media/how-to-connect-password-hash-synchronization/arch1.png)

암호 동기화를 통해 암호의 수를 줄여서 사용자가 하나의 암호를 유지할 수 있습니다. 암호 해시 동기화는 다음을 수행할 수 있습니다.

* 사용자의 생산성 향상.
* 지원 센터 비용 절감.  

암호 해시 동기화를 사용하면 하이브리드 계정에 대해 [유출 자격 증명 검색](../identity-protection/concept-identity-protection-risks.md#user-linked-detections)이 가능합니다. Microsoft는 다크 웹 연구원 및 법 집행 기관과 협력하여 공개적으로 사용 가능한 사용자 이름/암호 쌍을 찾습니다. 이러한 쌍 중 하나라도 사용자와 일치하는 쌍이 있으면 관련 계정이 높은 위험으로 이동됩니다. 

>[!NOTE]
> PHS를 활성화한 후 발견된 새로 유출된 자격 증명만 테넌트에 대해 처리됩니다. 이전에 찾은 자격 증명 쌍에 대한 확인은 수행되지 않습니다.


필요에 따라 로그인 메서드처럼 [AD FS(Active Directory Federation Services)와 페더레이션](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)을 사용하도록 선택하는 경우 백업으로 암호 해시 동기화를 설정할 수 있습니다.

사용자 환경에서 암호 해시 동기화를 사용하려면 다음을 수행해야 합니다.

* Azure AD Connect 설치.  
* 온-프레미스 Active Directory 인스턴스 및 Azure Active Directory 인스턴스 간에 디렉터리 동기화 구성.
* 암호 해시 동기화 사용.



자세한 내용은 [하이브리드 ID란?](whatis-hybrid-identity.md)을 참조하세요.




## <a name="next-steps"></a>다음 단계

- [하이브리드 ID란?](whatis-hybrid-identity.md)
- [Azure AD Connect 및 Connect Health란?](whatis-azure-ad-connect.md)
- [PTA(통과 인증)란?](how-to-connect-pta.md)
- [페더레이션이란?](whatis-fed.md)
- [Single Sign-On이란?](how-to-connect-sso.md)
- [암호 해시 동기화 작동 방식](how-to-connect-password-hash-synchronization.md)
