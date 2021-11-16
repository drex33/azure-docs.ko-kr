---
title: Azure AD의 중요한 작업 보고서 통합 문서 | Microsoft Docs
description: 중요한 작업 보고서 통합 문서를 사용하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenho
editor: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2021
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: a44aa07539be94aa645c95a6cee544bf92468bd7
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990664"
---
# <a name="sensitive-operations-report-workbook"></a>중요한 작업 보고서 통합 문서

IT 관리자는 환경에서 손상을 식별하여 환경을 정상 상태로 유지할 수 있어야 합니다. 

중요한 작업 보고서 통합 문서는 사용자 환경의 손상을 나타낼 수 있는 의심스러운 애플리케이션 및 서비스 주체 작업을 식별하는 데 도움을 주기 위한 것입니다.


이 문서에서는 이 통합 문서의 개요를 제공합니다.


## <a name="description"></a>설명

![통합 문서 범주](./media/workbook-sensitive-operations-report/workbook-category.png)

이 통합 문서에서는 테넌트에서 수행되었으며 서비스 주체가 손상될 수 있는 최근의 중요한 작업을 식별합니다.

조직에서 Azure Monitor 통합 문서를 처음 사용하는 경우 통합 문서에 액세스하기 전에 Azure AD 로그인 및 감사 로그를 Azure Monitor와 통합해야 합니다. 이렇게 하면 최대 2년 동안 통합 문서를 사용하여 로그를 저장, 쿼리 및 시각화할 수 있습니다. Azure Monitor와 통합한 후에 만든 로그인 및 감사 이벤트만 저장되므로 통합 문서에는 해당 날짜 이전의 인사이트가 포함되지 않습니다. Azure Active Directory용 Azure Monitor 통합 문서의 필수 구성 요소에 대해 자세히 알아봅니다. 이전에 Azure AD 로그인 및 감사 로그를 Azure Monitor와 통합한 경우 통합 문서를 사용하여 과거 정보를 평가할 수 있습니다. 
 
 

## <a name="sections"></a>섹션

이 통합 문서는 4개의 섹션으로 나뉩니다.

![통합 문서 섹션](./media/workbook-sensitive-operations-report/workbook-sections.png)


- **수정된 애플리케이션 및 서비스 주체 자격 증명/인증 방법** - 이 보고서는 최근에 여러 서비스 주체 자격 증명을 변경한 행위자와 각 유형의 서비스 주체 자격 증명이 변경된 횟수에 플래그를 적용합니다.

- **서비스 주체에 부여된 새 권한** - 이 통합 문서는 최근에 서비스 주체에 부여된 OAuth 2.0 권한도 강조 표시합니다. 

- **서비스 주체에 대한 디렉터리 역할 및 그룹 멤버 자격 업데이트**



- **수정된 페더레이션 설정** - 이 보고서는 사용자 또는 애플리케이션에서 도메인의 페더레이션 설정을 수정하는 경우를 강조 표시합니다. 예를 들어 서명 인증서와 같은 새 ADFS(Active Directory Federated Service) TrustedRealm 개체가 도메인에 추가될 때 보고합니다. 도메인 페더레이션 설정을 수정하는 경우는 거의 없습니다. 




### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>수정된 애플리케이션 및 서비스 주체 자격 증명/인증 방법

공격자가 환경에서 지속성을 확보하는 가장 일반적인 방법 중 하나는 새 자격 증명을 기존 애플리케이션 및 서비스 주체에 추가하는 것입니다. 공격자는 자격 증명을 통해 대상 애플리케이션 또는 서비스 주체로 인증하여 권한이 있는 모든 리소스에 대한 액세스 권한을 부여할 수 있습니다.

이 섹션에는 검색하는 데 도움이 되는 다음 데이터가 포함되어 있습니다.

- 자격 증명 유형을 포함하여 앱 및 서비스 주체에 추가된 모든 새 자격 증명

- 상위 행위자 및 이러한 행위자가 수행한 자격 증명 수정의 양

- 모든 자격 증명 변경에 대한 타임라인



### <a name="new-permissions-granted-to-service-principals"></a>서비스 주체에 부여된 새 권한

공격자가 액세스 권한을 얻을 수 있는 높은 권한 세트가 있는 서비스 주체 또는 애플리케이션을 찾을 수 없는 경우 권한을 다른 서비스 주체 또는 앱에 추가하려고 시도하는 경우가 많습니다.

이 섹션에는 기존 서비스 주체에 부여하는 AppOnly 권한에 대한 분석이 포함되어 있습니다. 관리자는 Exchange Online, Microsoft Graph 및 Azure AD Graph를 포함하지만 이에 국한되지 않는 과도하게 높은 권한이 부여된 인스턴스를 조사해야 합니다.


### <a name="directory-role-and-group-membership-updates-for-service-principals"></a>서비스 주체에 대한 디렉터리 역할 및 그룹 멤버 자격 업데이트 

다른 방법은 새 권한을 기존 서비스 주체 및 애플리케이션에 추가하는 공격자의 논리에 따라 권한을 기존 디렉터리 역할 또는 그룹에 추가하는 것입니다.

이 섹션에는 서비스 주체 멤버 자격에 대한 모든 변경 내용에 대한 개요가 포함되어 있으며 높은 권한 역할 및 그룹에 대한 추가 사항을 검토해야 합니다.



### <a name="modified-federation-settings"></a>수정된 페더레이션 설정

환경에서 장기적 발판을 확보하기 위한 또 다른 일반적인 방법은 다음과 같습니다.

- 테넌트의 페더레이션된 도메인 트러스트를 수정합니다.
- 공격자가 제어하는 ​​추가 SAML IDP를 신뢰할 수 있는 인증 원본으로 추가합니다. 

이 섹션에는 다음 데이터가 포함되어 있습니다.

- 기존 도메인 페더레이션 트러스트에 수행된 변경 내용

- 새 도메인 및 트러스트 추가


  


## <a name="filters"></a>필터

이 단락에는 각 섹션에 지원되는 필터가 나와 있습니다.


### <a name="modified-application-and-service-principal-credentialsauthentication-methods"></a>수정된 애플리케이션 및 서비스 주체 자격 증명/인증 방법

- 시간 범위
- 작업 이름
- 자격 증명
- 행위자
- 행위자 제외


### <a name="new-permissions-granted-to-service-principals"></a>서비스 주체에 부여된 새 권한

- 시간 범위
- 클라이언트 앱
- 리소스

### <a name="directory-role-and-group-membership-updates-to-service-principals"></a>서비스 주체에 대한 디렉터리 역할 및 그룹 멤버 자격 업데이트

- 시간 범위
- 작업
- 사용자 또는 앱 시작

### <a name="modified-federation-settings"></a>수정된 페더레이션 설정

- 시간 범위
- 작업
- 사용자 또는 앱 시작




## <a name="best-practices"></a>모범 사례


**사용:**
 
- **수정된 애플리케이션 및 서비스 주체 자격 증명** - 조직에서 자주 사용되지 않는 서비스 주체에 추가되는 자격 증명을 찾는 데 사용합니다. 이 섹션에 있는 필터를 사용하여 수정된 의심스러운 행위자 또는 서비스 주체를 추가로 조사합니다.


- **서비스 주체에 부여된 새 권한** - 행위자가 손상될 수 있는 서비스 주체에 추가하는 광범위하거나 과도한 권한을 찾는 데 사용합니다.  

- **수정된 페더레이션 설정** 섹션 - 추가되거나 수정된 대상 도메인/URL이 합법적인 관리자 동작인지 확인하는 데 사용합니다. 도메인 페더레이션 트러스트를 수정하거나 추가하는 작업은 거의 수행되지 않으며, 가능한 한 빨리 조사할 수 있도록 충실도가 높은 것으로 처리해야 합니다.





## <a name="next-steps"></a>다음 단계

- [Azure AD 통합 문서 사용 방법](howto-use-azure-monitor-workbooks.md)
