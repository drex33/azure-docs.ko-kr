---
title: 애플리케이션 관리의 PowerShell 샘플
titleSuffix: Azure AD
description: 이러한 PowerShell 샘플은 Azure Active Directory 테넌트에서 관리하는 앱에 사용됩니다. 이러한 샘플 스크립트를 사용하여 비밀 및 인증서에 대한 만료 정보를 찾을 수 있습니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: davidmu
ms.reviewer: sureshja
ms.openlocfilehash: 95f0ee8106612dd2035f0e1c77d9fe5d3efe2540
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620035"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>애플리케이션 관리용 Azure Active Directory PowerShell 예제

다음 표에는 Azure AD 애플리케이션 관리의 PowerShell 스크립트 예제에 대한 링크가 포함되어 있습니다. 이러한 샘플에는 다음 중 하나가 필요합니다.

- [AzureAD V2 PowerShell for Graph 모듈](/powershell/azure/active-directory/install-adv2) 또는
- 달리 명시되지 않는 한 [AzureAD V2 PowerShell for Graph 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true).

이러한 샘플에 사용된 cmdlet에 대한 자세한 내용은 [애플리케이션](/powershell/module/azuread/#applications)을 참조하세요.

| 링크 | 설명 |
|---|---|
|**애플리케이션 관리 스크립트**||
| [비밀 및 인증서 내보내기(앱 등록)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Azure Active Directory 테넌트에서 앱 등록용 비밀 및 인증서를 내보냅니다. |
| [비밀 및 인증서 내보내기(엔터프라이즈 앱)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Azure Active Directory 테넌트에서 엔터프라이즈 앱용 비밀 및 인증서를 내보냅니다. |
| [만료되는 비밀 및 인증서 내보내기](scripts/powershell-export-apps-with-expriring-secrets.md) | Azure Active Directory 테넌트에서 만료되는 비밀 및 인증서와 해당 소유자가 있는 앱 등록을 내보냅니다. |
| [필수 날짜 이후에 만료되는 비밀 및 인증서 내보내기](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Azure Active Directory 테넌트의 필수 날짜 이후에 만료되는 비밀 및 인증서가 있는 앱 등록을 내보냅니다. 이는 비 대화형 Client_Credentials Oauth 흐름을 사용합니다. |
