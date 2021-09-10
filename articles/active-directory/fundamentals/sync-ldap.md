---
title: Azure Active Directory를 사용하여 LDAP 동기화
description: Azure Active Directory를 사용하여 LDAP 동기화를 수행하는 방법에 관한 아키텍처 지침입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 224b0685b922e6ddd833637ddc1b628f0479ec70
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112200819"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Azure Active Directory를 사용하여 LDAP 동기화

LDAP(Lightweight Directory Access Protocol)는 TCP/IP 스택에서 실행되는 디렉터리 서비스 프로토콜입니다. 인터넷 디렉터리 연결, 검색, 수정에 사용되는 메커니즘을 제공합니다. LDAP 디렉터리 서비스는 클라이언트-서버 모델을 기반으로 하며 해당 기능은 기존 디렉터리에 대한 액세스를 가능하게 하는 것입니다. 대부분의 회사는 온-프레미스 LDAP 서버를 사용하여 중요한 비즈니스 앱의 사용자와 그룹을 저장합니다. 

Azure AD(Azure Active Directory)는 Azure AD Connect를 사용하여 LDAP 동기화를 대체할 수 있습니다. Azure AD Connect 동기화 서비스는 온-프레미스 환경과 Azure AD 간 ID 데이터 동기화에 관련된 모든 작업을 수행합니다. 

## <a name="use-when"></a>적용 가능한 상황

온-프레미스 LDAP v3 디렉터리와 Azure AD 간에 ID 데이터를 동기화해야 합니다. 

![아키텍처 다이어그램](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 사용자와 암호를 정렬하기 위해 LDAP v3 디렉터리를 사용하는 애플리케이션에 액세스합니다.

* **웹 브라우저**: 애플리케이션의 외부 URL에 액세스하기 위해 사용자가 상호 작용하는 구성 요소입니다.

* **웹앱**: LDAP v3 디렉터리에 대한 종속성이 있는 애플리케이션입니다.

* **Azure AD**: Azure AD는 Azure AD Connect를 통해 조직의 온-프레미스 LDAP 디렉터리에서 ID 정보(사용자, 그룹, 암호)를 동기화합니다. 

* **Azure AD Connect**: 온-프레미스 ID 인프라를 Microsoft Azure AD에 연결하기 위한 도구입니다. 마법사 및 안내 방식 환경을 통해 연결에 필요한 필수 조건과 구성 요소를 배포하고 구성할 수 있습니다. 

* **사용자 지정 커넥터**: 일반 LDAP 커넥터를 사용하여 Azure AD Connect 동기화 서비스를 LDAP v3 서버와 통합할 수 있습니다. LDAP 커넥터는 Azure AD Connect 위에 있습니다.

* **Active Directory**: Active Directory는 Windows Server 운영 체제 대부분에 포함된 디렉터리 서비스입니다. Active Directory 디렉터리 서비스를 실행하는 서버를 도메인 컨트롤러라고 하며 해당 서버는 Windows 도메인의 모든 사용자와 컴퓨터를 인증하고 권한을 부여합니다.

* **LDAP v3 서버**: 디렉터리 서비스 인증에 사용되는 회사 사용자와 암호를 저장하는 LDAP 프로토콜 규격 디렉터리입니다.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Azure AD를 사용하여 LDAP 동기화 구현

* [하이브리드 ID 디렉터리 통합 도구](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Azure AD Connect 설치 로드맵](../hybrid/how-to-connect-install-roadmap.md) 

* [LDAP 커넥터 개요 및 만들기](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > LDAP 커넥터를 배포하려면 고급 구성이 필요하며 이 커넥터는 제한된 지원으로 제공됩니다. 이 커넥터를 구성하려면 Microsoft Identity Manager 및 특정 LDAP 디렉터리에 대해 잘 알고 있어야 합니다. 
   >
   > 프로덕션 환경에서 이 구성을 배포해야 하는 고객은 이 구성에 대한 도움말, 지침 및 지원을 위해 Microsoft Consulting Services와 같은 파트너와 함께 작업하는 것이 좋습니다.
