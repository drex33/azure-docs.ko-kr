---
title: 조건부 액세스 서비스 종속성 - Azure Active Directory
description: Azure Active Directory 조건부 액세스에서 조건을 사용하여 정책을 트리거하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4174ea12f687e370eb1ff5508876e44a1430cdfb
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864023"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스의 서비스 종속성이란? 

조건부 액세스 정책을 사용하여 웹 사이트 및 서비스에 대한 액세스 요구 사항을 지정할 수 있습니다. 예를 들어 액세스 요구 사항에는 MFA(다단계 인증) 또는 [관리형 디바이스](require-managed-devices.md) 요구가 포함될 수 있습니다. 

사이트 또는 서비스에 직접 액세스하는 경우 일반적으로 관련 정책의 영향을 쉽게 평가할 수 있습니다. 예를 들어 SharePoint Online에 대해 MFA(다단계 인증)를 요구하는 정책이 구성되어 있으면 SharePoint 웹 포털에 로그인할 때마다 MFA가 적용됩니다. 그러나 다른 클라우드 앱에 대한 종속성이 있는 클라우드 앱이 있으므로 정책의 영향 평가가 항상 간단한 것은 아닙니다. 예를 들어 Microsoft Teams는 SharePoint Online의 리소스에 대한 액세스를 제공할 수 있습니다. 따라서 현재 시나리오에서 Microsoft Teams에 액세스하는 경우 SharePoint MFA 정책도 적용됩니다. 

> [!TIP]
> [Office 365](concept-conditional-access-cloud-apps.md#office-365) 앱을 사용하는 경우 Office 스택의 서비스 종속성과 관련된 이슈를 방지하기 위해 모든 Office 앱이 대상으로 지정됩니다.

## <a name="policy-enforcement"></a>정책 적용 

서비스 종속성이 구성되어 있으면 초기 바인딩된 적용이나 런타임에 바인딩된 적용을 사용하여 정책을 적용할 수 있습니다. 

- **초기 바인딩된 정책 적용** 은 사용자가 호출 앱에 액세스하기 전에 종속 서비스 정책을 충족해야 함을 의미합니다. 예를 들어 사용자가 MS Teams에 로그인하기 전에 SharePoint 정책을 충족해야 합니다. 
- **런타임에 바인딩된 정책 적용** 은 사용자가 호출 앱에 로그인한 후에 발생합니다. 호출 앱이 다운스트림 서비스용 토큰을 요청할 때까지 적용이 지연됩니다. 예를 들어 MS Teams에서 Planner에 액세스하는 경우, Office.com에서 SharePoint에 액세스하는 경우 등이 있습니다. 

아래 다이어그램은 MS Teams 서비스 종속성을 보여 줍니다. 실선 화살표는 초기 바인딩된 적용을 나타내고, Planner의 파선 화살표는 런타임에 바인딩된 적용을 나타냅니다. 

![MS Teams 서비스 종속성](./media/service-dependencies/01.png)

가능한 경우 항상 관련 앱과 서비스 전체에 공통 정책을 설정하는 것이 좋습니다. 일관된 보안 태세를 유지하면 최상의 사용자 환경이 제공됩니다. 예를 들어 Exchange Online, SharePoint Online, Microsoft Teams, 비즈니스용 Skype에서 공통 정책을 설정하면 다운스트림 서비스에 다른 정책이 적용됨으로써 발생할 수 있는 예기치 않은 프롬프트가 훨씬 줄어듭니다. 

Office 스택의 애플리케이션에서 이 작업을 수행하는 좋은 방법은 개별 애플리케이션을 대상으로 지정하는 대신 [Office 365 앱](concept-conditional-access-cloud-apps.md#office-365)을 사용하는 것입니다.

아래 표에는 클라이언트 앱이 충족해야 하는 추가 서비스 종속성이 나와 있습니다.  

| 클라이언트 앱         | 다운스트림 서비스                          | 적용 |
| :--                 | :--                                         | ---         | 
| Azure 데이터 레이크     | Microsoft Azure 관리(포털 및 API) | 초기 바인딩 |
| Microsoft Classroom | Exchange                                    | 초기 바인딩 |
|                     | SharePoint                                  | 초기 바인딩 |
| Microsoft Teams     | Exchange                                    | 초기 바인딩 |
|                     | MS Planner                                  | 런타임에 바인딩  |
|                     | Microsoft Stream                            | 런타임에 바인딩  |
|                     | SharePoint                                  | 초기 바인딩 |
|                     | 비즈니스 온라인용 Skype                   | 초기 바인딩 |
| Office 포털       | Exchange                                    | 런타임에 바인딩  |
|                     | SharePoint                                  | 런타임에 바인딩  |
| Outlook Groups      | Exchange                                    | 초기 바인딩 |
|                     | SharePoint                                  | 초기 바인딩 |
| Power Apps           | Microsoft Azure 관리(포털 및 API) | 초기 바인딩 |
|                     | Windows Azure Active Directory              | 초기 바인딩 |
| 프로젝트             | Dynamics CRM                                | 초기 바인딩 |
| 비즈니스용 Skype  | Exchange                                    | 초기 바인딩 |
| Visual Studio       | Microsoft Azure 관리(포털 및 API) | 초기 바인딩 |
| Microsoft Forms     | Exchange                                    | 초기 바인딩 |
|                     | SharePoint                                  | 초기 바인딩 |
| Microsoft To-Do     | Exchange                                    | 초기 바인딩 |

## <a name="next-steps"></a>다음 단계

사용자 환경에서 조건부 액세스를 구현하는 방법은 [Azure Active Directory에서 조건부 액세스 배포 계획](plan-conditional-access.md)을 참조하세요.
