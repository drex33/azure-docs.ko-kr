---
title: Azure AD의 조건부 액세스 간격 분석기 통합 문서 | Microsoft Docs
description: 조건부 액세스 간격 분석기 통합 문서를 사용하는 방법을 알아봅니다.
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
ms.openlocfilehash: bd62dbd1fc279e2ed26472b1c0a0464b56be92c0
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990674"
---
# <a name="conditional-access-gap-analyzer-workbook"></a>조건부 액세스 간격 분석기 통합 문서

Azure AD에서는 조건부 액세스 정책을 구성하여 리소스에 대한 액세스를 보호할 수 있습니다.
IT 관리자는 리소스가 제대로 보호되도록 조건부 액세스 정책이 예상대로 작동하는지 확인하려고 합니다. 조건부 액세스 간격 분석기 통합 문서를 사용하면 조건부 액세스 구현에서 간격을 검색할 수 있습니다.  

이 문서에서는 이 통합 문서의 개요를 제공합니다.


## <a name="description"></a>설명

![통합 문서 범주](./media/workbook-conditional-access-gap-analyzer/workbook-category.png)

IT 관리자는 적합한 사용자만 리소스에 액세스할 수 있도록 하려고 합니다. 이 목표는 Azure AD 조건부 액세스를 통해 달성할 수 있습니다.  

조건부 액세스 간격 분석기 통합 문서를 사용하면 조건부 액세스 정책이 예상대로 작동하는지 확인할 수 있습니다.

**이 통합 문서:**

- 조건부 액세스 정책이 적용되지 않은 사용자 로그인을 강조 표시합니다. 
- 조건부 액세스 정책에서 의도치 않게 제외된 사용자, 애플리케이션 또는 위치가 없는지 확인할 수 있습니다.  

 

## <a name="sections"></a>섹션


통합 문서에는 다음과 같은 4개의 섹션이 있습니다.  

- 레거시 인증을 사용하여 로그인하는 사용자 

- 조건부 액세스 정책의 영향을 받지 않는 애플리케이션의 로그인 수 

- 조건부 액세스 정책을 무시하는 고위험 로그인 이벤트 

- 조건부 액세스 정책의 영향을 받지 않은 위치별 로그인 수 


![위치별 조건부 액세스 적용 범위](./media/workbook-conditional-access-gap-analyzer/conditianal-access-by-location.png)

이러한 각 추세는 사용자 수준에 대한 로그인 분석을 제공하므로 조건부 액세스를 무시하는 시나리오당 사용자를 확인할 수 있습니다. 

## <a name="filters"></a>필터

이 통합 문서는 시간 범위 필터 설정을 지원합니다.

![시간 범위 필터](./media/workbook-conditional-access-gap-analyzer/time-range.png)



## <a name="best-practices"></a>모범 사례

이 통합 문서를 사용하여 테넌트가 다음과 같은 조건부 액세스 모범 사례로 구성되었는지 확인합니다.  

- 모든 레거시 인증 로그인 차단 

- 모든 애플리케이션에 하나 이상의 조건부 액세스 정책 적용 

- 모든 고위험 로그인 차단  

- 신뢰할 수 없는 위치로부터의 로그인 차단  

 





## <a name="next-steps"></a>다음 단계

- [Azure AD 통합 문서 사용 방법](howto-use-azure-monitor-workbooks.md)
