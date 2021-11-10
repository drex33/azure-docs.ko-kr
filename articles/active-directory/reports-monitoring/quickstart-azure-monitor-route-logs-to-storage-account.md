---
title: 자습서 - 스토리지 계정에 디렉터리 로그 보관 | Microsoft Docs
description: 스토리지 계정으로 Azure Active Directory 로그를 푸시하도록 Azure Diagnostics를 설정하는 방법 알아보기
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/05/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f170cd4f3530ea7391d2fbd039263c29540321f8
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995467"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>자습서: Azure Storage 계정에 Azure AD 로그 보관

이 자습서에서는 Azure AD(Azure Active Directory) 로그를 Azure Storage 계정으로 라우팅하도록 Azure Monitor 진단 설정을 지정하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항 

이 기능을 사용하려면 다음이 필요합니다.

* Azure 구독과 Azure Storage 계정. Azure 구독이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* Azure AD 테넌트.
* Azure AD 테넌트의 *글로벌 관리자* 또는 *보안 관리자* 인 사용자.

## <a name="archive-logs-to-an-azure-storage-account"></a>Azure Storage 계정에 로그 보관

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. **Azure Active Directory** > **모니터링** > **감사 로그** 를 선택합니다. 

3. **데이터 내보내기 설정** 을 선택합니다. 

4. **진단 설정** 창에서 다음 중 하나를 수행합니다.
    1. 기존 설정을 변경하려면 업데이트할 진단 설정 옆에 있는 **설정 편집** 을 선택합니다.
    1. 새 설정을 추가하려면 **진단 설정 추가** 를 선택합니다.  

    최대 세 개의 설정을 지정할 수 있습니다.

     ![설정 내보내기](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. 새 설정을 만드는 경우 **진단 설정** 창에서 설정의 이름을 입력하여 해당 용도를 알려줍니다(예: *Azure 스토리지 계정으로 보내기*). 기존 설정의 이름은 변경할 수 없습니다.

6. **대상 세부 정보** 에서 **스토리지 계정에 보관** 확인란을 선택합니다. 

7. **구독** 드롭다운 메뉴에서 Azure 구독을 선택하고 로그를 라우팅할 **스토리지 계정** 드롭다운 메뉴에서 스토리지 계정을 선택합니다.

8. **범주 세부 정보** 아래에서 모든 관련 범주를 선택합니다.

    다음 중 하나 또는 둘 모두를 수행합니다.
    1. 스토리지 계정에 감사 로그를 보내려면 **AuditLogs** 확인란을 선택합니다.
    
    1. 스토리지 계정에 로그인 로그를 보내려면 **SignInLogs** 확인란을 선택합니다.

    ![진단 설정](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

9. 범주를 선택한 후에는 **보존 일** 필드에 로그 데이터에 필요한 보존 기간(일)을 입력합니다. 기본적으로 이 값은 *0* 이며, 로그가 스토리지 계정에 무기한 보존된다는 뜻입니다. 다른 값으로 설정하면 선택된 일수보다 오래된 이벤트는 자동으로 정리됩니다.
 
10. **저장** 을 선택하여 설정을 저장합니다.

11. 진단 설정 창으로 돌아가려면 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 감사 로그 스키마 해석](./overview-reports.md)
* [Azure Monitor에서 로그인 로그 스키마 해석](reference-azure-monitor-sign-ins-log-schema.md)
* [질문과 대답 및 알려진 문제](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
