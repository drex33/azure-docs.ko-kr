---
title: 사용량 및 인사이트 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 사용량 및 인사이트 보고서 소개
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f9c7011a142f521b33fa8faea830cde90e1ae70a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995809"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 사용량 및 인사이트 보고서

사용량 및 인사이트 보고서를 사용하여 로그인 데이터의 애플리케이션 중심 보기를 가져올 수 있습니다. 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

*   조직에서 가장 많이 사용되는 애플리케이션은 무엇인가요?
*   로그인 실패가 가장 많은 애플리케이션은 무엇인가요? 
*   각 애플리케이션에서 가장 일반적인 로그인 오류는 무엇인가요?

## <a name="prerequisites"></a>전제 조건 

사용량 및 인사이트 보고서의 데이터에 액세스하려면 다음과 같은 사항이 필요합니다.

* Azure AD 테넌트
* 로그인 데이터를 볼 수 있는 Azure AD Premium(P1/P2) 라이선스
* 전역 관리자, 보안 관리자, 보안 읽기 권한자 또는 보고서 읽기 권한자 역할이 있는 사용자. 또한 모든 사용자(비관리자)가 자신의 로그인에 액세스할 수 있습니다. 

## <a name="access-the-usage-and-insights-report"></a>사용량 및 인사이트 보고서 액세스

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 올바른 디렉터리를 선택한 다음 **Azure Active Directory** 를 선택하고 **엔터프라이즈 애플리케이션** 을 선택합니다.
3. **활동** 섹션에서, **사용량 & 인사이트** 를 선택하여 보고서를 확인합니다. 

![스크린샷은 활동 섹션에서 선택한 사용량 & 인사이트를 보여줍니다.](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>보고서 사용

사용량 및 인사이트 보고서에는 로그인 시도가 하나 이상 포함된 애플리케이션 목록이 표시되며 성공한 로그인 횟수, 실패한 로그인 횟수 및 성공률을 기준으로 정렬할 수 있습니다.

목록의 맨 아래에서 **더 보기** 를 클릭하면 페이지에서 추가 애플리케이션을 볼 수 있습니다. 날짜 범위를 선택하여 범위 내에 사용된 모든 애플리케이션을 볼 수 있습니다.

![스크린샷은 범위를 선택하고 다양한 앱의 로그인 활동을 볼 수 있는 애플리케이션 활동 사용량 & 인사이트를 보여줍니다.](./media/concept-usage-insights-report/usage-and-insights-report.png)

특정 애플리케이션에 포커스를 설정할 수도 있습니다. **로그인 활동 보기** 를 선택하여 애플리케이션에 대한 시간에 따른 로그인 활동 및 상위 오류를 확인합니다.  

애플리케이션 사용량 그래프에서 날짜를 선택하면 로그인 활동의 자세한 목록이 표시됩니다.  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="스크린샷은 로그인 활동에 대한 그래프를 볼 수 있는 특정 애플리케이션에 대한 사용량과 인사이트를 보여 줍니다.":::

## <a name="next-steps"></a>다음 단계

* [로그인 보고서](concept-sign-ins.md)
