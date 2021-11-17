---
title: 앱을 Azure Active Directory로 마이그레이션하기 위한 리소스
description: 애플리케이션 액세스 및 인증을 Azure AD(Azure Active Directory)로 마이그레이션하는 데 도움이 되는 리소스입니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: b8aab437e9ffac19da56a892a4547aa234682458
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856584"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>애플리케이션을 Azure Active Directory로 마이그레이션하기 위한 리소스

애플리케이션 액세스 및 인증을 Azure AD(Azure Active Directory)로 마이그레이션하는 데 도움이 되는 리소스입니다.

| 리소스  | Description  |
|:-----------|:-------------|
|[앱을 Azure AD로 마이그레이션](https://aka.ms/migrateapps/whitepaper) | 이 백서에서는 마이그레이션의 이점을 제시하고 4가지 명확한 단계(탐색, 분류, 마이그레이션 및 지속적인 관리)로 마이그레이션을 계획하는 방법을 설명합니다. 프로세스에 대해 생각하고 프로젝트를 소비하기 쉬운 부분으로 나누는 방법이 안내됩니다. 문서 전체에는 과정에 도움이 되는 중요한 리소스에 대한 링크가 있습니다. |
|[개발자 자습서: 개발자를 위한 AD FS에서 Azure AD로의 애플리케이션 마이그레이션 플레이북](https://aka.ms/adfsplaybook) | 이 ASP.NET 코드 샘플 세트와 함께 제공되는 자습서는 AD FS(Active Directory Federation Services)와 통합된 애플리케이션을 Azure AD(Azure Active Directory)로 안전하게 마이그레이션하는 방법을 익히는 데 도움이 됩니다. 이 자습서는 AD FS와 Azure AD에서 앱을 구성하는 방법을 익히고 이 프로세스 중에 코드베이스에서 변경해야 하는 내용을 파악해야 하는 개발자에게 중점을 두고 있습니다.|
| [도구: Active Directory Federation Services 마이그레이션 준비 스크립트](https://aka.ms/migrateapps/adfstools) | 온-프레미스 AD FS(Active Directory Federation Services) 서버에서 실행하여 앱을 Azure AD로 마이그레이션할 준비가 되었는지 확인할 수 있는 스크립트입니다.|
| [배포 계획: AD FS에서 암호 해시 동기화로 마이그레이션](https://aka.ms/ADFSTOPHSDPDownload) | 암호 해시 동기화를 수행하면 사용자 암호의 해시는 온-프레미스 Active Directory에서 Azure AD로까지 동기화됩니다. 이를 통해 Azure AD는 온-프레미스 Active Directory와 상호 작용하지 않고 사용자를 인증할 수 있습니다.|
| [배포 계획: AD FS에서 통과 인증으로 마이그레이션](https://aka.ms/ADFSTOPTADPDownload)|Azure AD 통과 인증을 사용하면 사용자가 온-프레미스와 클라우드 기반 애플리케이션 둘 다에서 동일한 암호로 로그인할 수 있습니다. 이 기능은 사용자에게 더 나은 환경을 제공합니다. 기억할 암호가 하나 줄어들기 때문입니다. IT 기술 지원팀 비용이 감소됩니다. 사용자가 암호를 하나만 기억해도 되면 로그인 방법을 잊어버릴 가능성이 줄어들기 때문입니다. 사람들이 Azure AD를 사용하여 로그인할 때 이 기능은 온-프레미스 Active Directory에 대해 직접 사용자 암호의 유효성을 검사합니다.|
| [배포 계획: Azure AD로 SaaS 앱에 Single Sign-On을 사용하도록 설정](https://aka.ms/SSODPDownload) | SSO(Single Sign-On)는 단일 사용자 계정을 사용하여 한 번만 로그인하면 비즈니스 수행에 필요한 모든 앱과 리소스에 액세스할 수 있게 해줍니다. 예를 들어 사용자가 로그인한 후에는 두 번째 인증(예: 암호 입력)을 거치지 않고 Microsoft Office에서 SalesForce로, Box로 이동할 수 있습니다.
| [배포 계획: 애플리케이션 프록시를 통해 앱을 Azure AD로 확장](https://aka.ms/AppProxyDPDownload)| 직원의 랩톱 및 기타 디바이스에서 온-프레미스 애플리케이션에 액세스할 수 있도록 하려면 일반적으로 VPN(가상 사설망) 또는 완충 영역(DMZ)이 필요했습니다. 이러한 솔루션은 복잡하고 안전하게 만들기도 어려울 뿐만 아니라 설정과 관리에도 비용이 많이 듭니다. Azure AD 애플리케이션 프록시를 사용하면 온-프레미스 애플리케이션에 쉽게 액세스할 수 있습니다. |
| [배포 계획](../fundamentals/active-directory-deployment-plans.md) | 다단계 인증, 조건부 액세스, 사용자 프로비저닝, 원활한 SSO, 셀프 서비스 암호 재설정 등의 기능 배포에 대한 더 많은 배포 계획을 찾아보세요. |
| [Symantec SiteMinder에서 Azure AD로 앱 마이그레이션](https://azure.microsoft.com/mediahandler/files/resourcefiles/migrating-applications-from-symantec-siteminder-to-azure-active-directory/Migrating-applications-from-Symantec-SiteMinder-to-Azure-Active-Directory.pdf) | Symantec SiteMinder에서 Azure AD로 애플리케이션을 마이그레이션하는 과정을 안내하는 예제를 통해 애플리케이션 마이그레이션 및 통합 옵션에 대한 단계별 지침을 얻습니다. |

