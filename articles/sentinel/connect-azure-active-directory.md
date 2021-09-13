---
title: Azure Sentinel에 Azure Active Directory 데이터 연결 | Microsoft Docs
description: Azure Active Directory에서 데이터를 수집하고 Azure AD 로그인, 감사 및 프로비저닝 로그를 Azure Sentinel로 스트리밍하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 20e9920921afe1eae8babb76212a6de7ea2e1321
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251852"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Azure Sentinel에 Azure AD(Azure Active Directory) 데이터 연결

> [!IMPORTANT]
> 아래 표시된 대로 사용 가능한 로그 유형 중 일부는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel의 기본 제공 커넥터를 사용하여 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)에서 데이터를 수집하고 Azure Sentinel로 스트리밍할 수 있습니다. 커넥터를 사용하면 다음 로그 유형을 스트리밍할 수 있습니다.

- [**로그인 로그**](../active-directory/reports-monitoring/concept-all-sign-ins.md): 사용자가 인증 요소를 제공하는 대화형 사용자 로그인에 대한 정보를 포함합니다.

    이제 Azure AD 커넥터에는 현재 모두 **미리 보기** 로 제공되는 다음과 같은 세 가지 추가 범주의 로그인 로그가 포함되어 있습니다.
    
    - [**비 대화형 사용자 로그인 로그**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins): 사용자의 상호 작용 또는 인증 요소 없이 사용자를 대신하여 클라이언트가 수행하는 로그인에 대한 정보를 포함합니다.
    
    - [**서비스 주체 로그인 로그**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins): 사용자와 관련이 없는 앱 및 서비스 주체별 로그인에 대한 정보를 포함합니다. 이러한 로그인에서 앱 또는 서비스는 리소스를 인증하거나 액세스할 수 있는 자격 증명을 자체적으로 제공합니다.
    
    - [**관리 ID 로그인 로그**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins): Azure에서 관리하는 비밀이 있는 Azure 리소스의 로그인에 대한 정보를 포함합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

- [**감사 로그**](../active-directory/reports-monitoring/concept-audit-logs.md): 사용자 및 그룹 관리, 관리형 애플리케이션 및 디렉터리 작업과 관련된 시스템 작업에 대한 정보를 포함합니다.

- [**프로비저닝 로그**](../active-directory/reports-monitoring/concept-provisioning-logs.md)(또한 **미리 보기** 로 제공): Azure AD 프로비저닝 서비스에서 프로비전된 사용자, 그룹 및 역할에 대한 시스템 작업 정보를 포함합니다. 


## <a name="prerequisites"></a>사전 요구 사항

- 로그인 로그를 Azure Sentinel에 수집하려면 Azure Active Directory P1 또는 P2 라이선스가 필요합니다. 모든 Azure AD 라이선스(Free/O365/P1/P2)는 다른 로그 유형을 수집하기에 충분합니다. Azure Monitor(Log Analytics)와 Azure Sentinel에 대해 기가바이트 당 요금이 추가 적용될 수 있습니다.

- 작업 영역에서 사용자에게 Azure Sentinel 참가자 역할을 할당해야 합니다.

- 사용자에게 로그를 스트리밍할 테넌트의 전역 관리자 또는 보안 관리자 역할이 할당되어야 합니다.

- 연결 상태를 확인하려면 사용자에게 Azure AD 진단 설정에 대한 읽기 및 쓰기 권한이 있어야 합니다. 

## <a name="connect-to-azure-active-directory"></a>Azure Active Directory에 연결

1. Azure Sentinel의 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 **Azure Active Directory** 를 선택한 다음, **커넥터 페이지 열기** 를 선택합니다.

1. Azure Sentinel로 스트리밍할 로그 유형 옆에 있는 확인란을 표시(위 참조)하고 **연결** 을 선택합니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공하면 데이터는 다음 테이블의 **LogManagement** 섹션에 있는 **로그** 에 표시됩니다.

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Azure AD 로그를 쿼리하려면 쿼리 창의 맨 위에 관련 테이블 이름을 입력합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Active Directory를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
