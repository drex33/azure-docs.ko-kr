---
title: Azure Active Directory의 로그를 다운로드하는 방법 | Microsoft Docs
description: Azure Active Directory에서 활동 로그를 다운로드하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/14/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bbf95fe288bb81ac8a1580e56244bea11e52821
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995070"
---
# <a name="how-to-download-logs-in-azure-active-directory"></a>방법: Azure Active Directory에서 로그 다운로드

Azure AD(Azure Active Directory) 포털에서는 다음 세 가지 유형의 활동 로그에 액세스할 수 있습니다.

- **[로그인](concept-sign-ins.md)** – 로그인 및 사용자가 리소스를 사용하는 방법에 대한 정보입니다.
- **[감사](concept-audit-logs.md)** – 사용자 및 그룹 관리 또는 테넌트 리소스에 적용된 업데이트와 같이 테넌트에 적용된 변경 사항에 대한 정보입니다.
- **[프로비저닝](concept-provisioning-logs.md)** – ServiceNow의 그룹 생성 또는 Workday에서 가져온 사용자와 같이 프로비저닝 서비스에서 수행하는 활동입니다.

Azure AD는 제한된 시간 동안 이러한 로그에 데이터를 저장합니다. IT 관리자는 활동 로그를 다운로드하여 장기 백업을 할 수 있습니다.

이 문서에서는 Azure AD에서 활동 로그를 다운로드하는 방법을 설명합니다.  

## <a name="what-you-should-know"></a>알아야 할 사항

- Azure AD 포털에서 활동 로그에 대한 여러 진입점을 찾을 수 있습니다. 예를 들어 [사용자](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers) 또는 [그룹](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) 페이지의 **활동** 섹션입니다. 그러나 처음에 필터링되지 않은 로그 보기를 제공하는 위치는 [Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 페이지의 **모니터링** 섹션뿐입니다.    

- Azure AD는 특정 기간 동안만 활동 로그를 저장합니다. 자세한 내용은 [Azure AD는 보고 데이터를 얼마나 오래 저장하나요?](reference-reports-data-retention.md)를 참조하세요. 

- 로그를 다운로드하여 로그가 저장되는 길이를 제어할 수 있습니다. 

- 최대 250,000개 레코드를 다운로드할 수 있습니다. 더 많은 데이터를 다운로드하려면 보고 API를 사용하세요.

- 다운로드는 설정한 필터를 기반으로 합니다. 

- Azure AD는 다운로드에 대해 다음 형식을 지원합니다.

    - **CSV** 

    - **JSON** 

- 다운로드된 파일의 타임스탬프는 항상 UTC 기준입니다.



## <a name="what-license-do-you-need"></a>어떤 라이선스가 필요하나요?

활동 로그의 데이터를 다운로드하는 옵션은 모든 버전의 Azure AD에서 사용할 수 있습니다.

Microsoft Graph를 사용하여 활동 로그를 다운로드할 수도 있습니다. 그러나 로그를 문법적으로 다운로드하려면 프리미엄 라이선스가 필요합니다.


## <a name="who-can-do-it"></a>누가 수행할 수 있나요?

전역 관리자가 작동하는 동안 이 작업을 수행하기 위해 더 낮은 권한이 있는 계정을 사용해야 합니다. 감사 로그에 액세스하기 위해 다음 역할이 작동합니다.

- 보고서 읽기 권한자
- 전역 Reader
- 보안 관리자
- 보안 Reader


## <a name="how-to-do-it"></a>작업 방법


**활동 로그를 다운로드하려면:**

1. 관심 있는 활동 로그 보기로 이동합니다.
 
    - [로그인 로그](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)
    
    - [감사 로그](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)    
       
    - [프로비저닝 로그](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents)    
   

2.  필수 필터를 **추가** 합니다.  

    ![필터 추가](./media/\howto-download-logs/add-filter.png)    

3. 데이터를 **다운로드** 합니다.

    ![다운로드 로그](./media/\howto-download-logs/download-log.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD의 로그인 로그](concept-sign-ins.md)
- [Azure AD의 감사 로그](concept-audit-logs.md)