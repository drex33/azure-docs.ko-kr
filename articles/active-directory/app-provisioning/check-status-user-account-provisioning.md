---
title: Azure Active Directory에서 SaaS(Software as a Service) 애플리케이션으로 자동 사용자 계정 프로비저닝 보고
description: 자동 사용자 계정 프로비전 작업의 상태를 확인하는 방법과 개별 사용자의 프로비전 문제를 해결하는 방법에 대해 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: c4605372964012f67ff269d6bd9ebaa9a4030b67
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991568"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>자습서: 자동 사용자 계정 프로비전에 대한 보고

Azure AD(Azure Active Directory)에는 엔드투엔드 ID 수명 주기 관리를 위해 SaaS 앱 및 기타 시스템에서 사용자 계정을 자동으로 프로비저닝하거나 프로비저닝을 해제하는 데 유용한 [사용자 계정 프로비저닝 서비스](user-provisioning.md)가 포함되어 있습니다. Azure AD는 [여기](../saas-apps/tutorial-list.md)에 나오는 사용자 프로비저닝 자습서와 함께 모든 애플리케이션 및 시스템에 대해 사전 통합된 사용자 프로비저닝 커넥터를 지원합니다.

이 문서에서는 프로비전 작업을 설정한 후 해당 상태를 확인하는 방법과 개별 사용자 및 그룹의 프로비전 문제를 해결하는 방법에 대해 설명합니다.

## <a name="overview"></a>개요

프로비전 커넥터는 지원되는 애플리케이션에 [제공된 설명서](../saas-apps/tutorial-list.md)에 따라 [Azure Portal](https://portal.azure.com)을 사용하여 설정하고 구성합니다. 일단 구성되고 실행된 후에는 다음 두 가지 방법 중 하나를 사용하여 프로비전 작업을 보고할 수 있습니다.

* **Azure Portal** - 이 문서에서는 주로 지정된 애플리케이션에 대한 프로비저닝 요약 보고서와 자세한 프로비저닝 감사 로그를 제공하는 [Azure Portal](https://portal.azure.com)에서 보고서 정보를 검색하는 방법에 대해 설명합니다.
* **감사 API** - Azure Active Directory는 자세한 프로비저닝 감사 로그를 프로그래밍 방식으로 검색할 수 있게 해주는 감사 API도 제공합니다. 이 API의 사용과 관련하여 [Azure Active Directory 감사 API 참조](/graph/api/resources/directoryaudit) 문서를 참조하세요. 이 문서에서는 API를 사용하는 방법을 구체적으로 다루지 않지만, 감사 로그에 기록되는 프로비전 이벤트 유형에 대해서는 자세히 설명합니다.

### <a name="definitions"></a>정의

이 문서에서 사용하는 용어는 다음과 같이 정의됩니다.

* **원본 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 원본이 되는 사용자의 리포지토리입니다. Azure Active Directory는 대부분의 사전 통합된 프로비전 커넥터를 위한 원본 시스템이지만, Workday 인바운드 동기화와 같이 일부 예외가 있습니다.
* **대상 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 대상이 되는 사용자의 리포지토리입니다. 이 시스템은 일반적으로 SaaS 애플리케이션(예: Salesforce, ServiceNow, G Suite, Dropbox for Business)이지만, Active Directory와 같은 온-프레미스 시스템일 수도 있습니다(예: Active Directory에 대한 Workday 인바운드 동기화).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Azure Portal에서 프로비저닝 보고서 가져오기

지정된 애플리케이션에 대한 프로비저닝 보고서 정보를 가져오려면 먼저 **작업** 섹션에서 [Azure Portal](https://portal.azure.com) 및 **Azure Active Directory**&gt;**엔터프라이즈 앱**&gt;**프로비저닝 로그(미리 보기)** 를 시작합니다. 프로비저닝이 구성된 엔터프라이즈 애플리케이션으로 이동할 수도 있습니다. 예를 들어 사용자를 LinkedIn Elevate로 프로비전하는 경우 애플리케이션 세부 정보의 탐색 경로는 다음과 같습니다.

**Azure Active Directory &gt; 엔터프라이즈 애플리케이션 &gt; 모든 애플리케이션 &gt; LinkedIn Elevate**

여기에서 아래에 설명된 프로비저닝 진행률 표시줄과 프로비저닝 로그 둘 다에 액세스할 수 있습니다.

## <a name="provisioning-progress-bar"></a>프로비저닝 진행률 표시줄

[프로비저닝 진행률 표시줄](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)은 지정된 애플리케이션의 **프로비저닝** 탭에서 볼 수 있습니다. 이 진행률 표시줄은 **현재 상태** 섹션에 있으며 현재 초기 또는 증분 주기의 상태를 표시합니다. 이 섹션에는 다음 항목도 표시됩니다.

* 동기화되어 현재의 원본 시스템과 대상 시스템 간 프로비전에 해당하는 범위에 포함된 총 사용자 및/또는 그룹 수
* 동기화가 마지막으로 실행된 시간 - 일반적으로 [초기 주기](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)가 완료된 후 20-40분마다 동기화가 발생합니다.
* [초기 주기](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)가 완료되었는지 여부입니다.
* 프로비저닝 프로세스의 격리 여부 및 격리 상태에 대한 이유(예: 잘못된 관리자 자격 증명으로 인해 대상 시스템과 통신하지 못하는 경우).

**현재 상태** 는 프로비저닝 작업의 작동 상태를 확인하기 위해 관리자가 먼저 확인해야 합니다.

 ![요약 보고서](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>프로비저닝 로그(미리 보기)

프로비저닝 서비스에서 실행하는 모든 작업은 Azure AD [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)에 기록됩니다. **작업** 섹션에서 **Azure Active Directory** &gt; **엔터프라이즈 앱** &gt; **프로비저닝 로그(미리 보기)** 를 선택하여 Azure Portal에서 프로비저닝 로그에 액세스할 수 있습니다. 원본 시스템이나 대상 시스템에서 사용자의 이름 또는 식별자를 기준으로 프로비전 데이터를 검색할 수 있습니다. 자세한 내용은 [프로비저닝 로그(미리 보기)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)를 참조하세요. 기록되는 활동 이벤트 유형은 다음과 같습니다.

## <a name="troubleshooting"></a>문제 해결

프로비저닝 요약 보고서 및 프로비저닝 로그는 관리자가 다양한 사용자 계정 프로비저닝 문제를 해결하는 데 중요한 역할을 합니다.

자동 사용자 프로비전 문제를 해결하는 방법에 대한 시나리오 기반 지침은 [애플리케이션에 사용자를 구성 및 프로비전하는 문제](../app-provisioning/application-provisioning-config-problem.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)