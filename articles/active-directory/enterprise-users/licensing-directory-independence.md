---
title: 다중 테넌트 상호 작용의 특성 - Azure AD | Microsoft Docs
description: Azure Active Directory 조직의 데이터 독립성 이해
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7cd7550fa3e39669d60afa87a436c64bc85a959
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985925"
---
# <a name="understand-how-multiple-azure-active-directory-tenant-organizations-interact"></a>여러 Azure Active Directory 테넌트 조직 간의 상호 작용 방식 이해

Azure AD(Azure Active Directory)의 각 Azure AD 조직은 완전히 독립적입니다. 관리하는 다른 Azure AD 조직과 논리적으로 독립된 피어입니다. 조직 간 독립성에는 리소스 독립성, 관리 독립성 및 동기화 독립성이 포함됩니다. 또한 조직 간에는 부모-자식 관계가 없습니다.

## <a name="resource-independence"></a>리소스 독립성

* 외부 사용자를 부분적으로 제외하고 한 조직에서 Azure AD 리소스를 만들거나 삭제해도 다른 조직의 리소스에 영향을 주지 않습니다.
* 한 조직에서 도메인 이름 중 하나를 등록하는 경우 다른 조직에서 사용할 수 없습니다.

## <a name="administrative-independence"></a>관리 독립성

'Contoso' 조직의 관리자가 아닌 사용자가 테스트 조직 'Test'를 만들면 다음과 같이 수행됩니다.

* 기본적으로 조직을 만드는 사용자는 해당하는 새 조직에서 외부 사용자로 추가되고 해당 조직의 전역 관리자 역할이 할당됩니다.
* 'Test'의 관리자가 'Test' 조직에 대한 관리 권한을 특별히 부여하지 않으면 'Contoso' 조직의 관리자는 이 디렉터리에 대한 직접 관리 권한이 없습니다. 그러나 'Contoso'의 관리자가 'Test'를 만든 사용자 계정에 로그인하는 경우 'Test' 조직에 대한 액세스를 제어할 수 있습니다.
* 한 조직에서 사용자에 대한 Azure AD 역할을 추가하거나 제거 하는 경우 해당 사용자가 다른 Azure AD 조직에서 할당된 역할에는 변경 내용이 적용되지 않습니다.

## <a name="synchronization-independence"></a>동기화 독립성

각 Azure AD 조직을 독립적으로 구성하여 다음 중 하나의 단일 인스턴스에서 데이터를 동기화할 수 있습니다.

* 데이터를 단일 AD 포리스트와 동기화하는 Azure AD Connect 도구
* 데이터를 하나 이상의 온-프레미스 포리스트 및/또는 Azure AD 이외의 데이터 원본과 동기화하는 Forefront Identity Manager용 Azure Active Directory 커넥터

## <a name="add-an-azure-ad-organization"></a>Azure AD 조직 추가

Azure Portal에서 Azure AD 조직을 추가하려면 Azure AD 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인하고 **새로 만들기** 를 선택합니다.

> [!NOTE]
> 다른 Azure 리소스와 달리 Azure AD 조직은 Azure 구독의 자식 리소스가 아닙니다. Azure 구독이 취소되거나 만료되는 경우 Azure PowerShell, Microsoft Graph API 또는 Microsoft 365 관리 센터를 사용하여 Azure AD 조직의 데이터에 계속 액세스할 수 있습니다. [다른 구독을 조직과 연결](../fundamentals/active-directory-how-subscriptions-associated-directory.md)할 수도 있습니다.
>

## <a name="next-steps"></a>다음 단계

Azure AD 라이선스 고려 사항 및 모범 사례는 [Azure Active Directory 라이선스란?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)을 참조하세요.
