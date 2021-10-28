---
title: 조건부 액세스 API 및 PowerShell - Azure Active Directory
description: Azure AD 조건부 액세스 API 및 PowerShell을 사용하여 코드와 같은 정책 관리
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4c6e36defc8b3e97f73133d2fb766e64a872fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618980"
---
# <a name="conditional-access-programmatic-access"></a>조건부 액세스: 프로그래밍 방식 액세스

대부분의 조직에서는 코드와 같이 최대한 많은 환경을 관리해야 하는 필요성을 표현했습니다. Microsoft Graph를 사용하여 사용자 환경에서 다른 코드 조각과 같은 조건부 액세스 정책을 처리할 수 있습니다.

Microsoft Graph는 조직에서 Microsoft 365, Windows 10, Enterprise Mobility + Security의 데이터와 상호 작용하는 데 사용할 수 있는 통합 프로그래밍 모델을 제공합니다. Microsoft Graph에 대한 자세한 내용은 [Microsoft Graph 개요](/graph/overview) 문서를 참조하세요.

![그래프의 일부인 기본 리소스 및 관계를 보여 주는 이미지](./media/howto-conditional-access-apis/microsoft-graph.png)

다음 예제는 지원 없이 있는 그대로 제공됩니다. 해당 예제를 조직에서 도구에 대한 기반으로 사용할 수 있습니다. 

다음의 많은 예제에서는 [관리 ID](../managed-identities-azure-resources/overview.md), [Logic Apps](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/), [Azure Key Vault](../../key-vault/general/overview.md) 등의 도구를 사용합니다.

## <a name="configure"></a>구성

### <a name="powershell"></a>PowerShell

많은 관리자의 경우 PowerShell은 이미 이해된 스크립팅 도구입니다. 다음 예제에서는 [Azure AD PowerShell 모듈](https://www.powershellgallery.com/packages/AzureAD)을 사용하여 조건부 액세스 정책을 관리하는 방법을 보여 줍니다.

- [Azure AD PowerShell 명령을 사용하여 조건부 액세스 정책 구성](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>그래프 API

이 예제에서는 조건부 액세스 그래프 API에서 사용할 수 있는 기본 CRUD(만들기, 읽기, 업데이트, 삭제) 옵션을 보여 줍니다. 예제에는 몇 가지 샘플 정책을 만드는 데 사용할 수 있는 몇 가지 JSON 템플릿도 포함되어 있습니다.

- [Microsoft Graph API 호출을 사용하여 조건부 액세스 정책 구성](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>템플릿을 사용하여 구성

조건부 액세스 API를 사용하여 사전 프로덕션 환경에서 템플릿을 사용해 조건부 액세스 정책을 배포합니다.

- [Microsoft Graph API 템플릿을 사용하여 조건부 액세스 정책 구성](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>테스트

이 예제에서는 사전 프로덕션과 같은 한 환경에서 프로덕션 환경과 같은 다른 환경으로 조건부 액세스 정책을 복사할 수 있는 승인 워크플로를 사용하여 보다 안전한 배포 방법을 모델링합니다.

- [테스트 환경에서 조건부 액세스 정책 승격](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>배포:

이 예제에서는 사용자 모집단에 대한 단계적 배포 조건부 액세스 정책을 점진적으로 수행하여 지원 영향을 관리하고 조기에 문제를 파악할 수 메커니즘을 제공합니다.

- [승인 워크플로를 사용하여 프로덕션 환경에 조건부 액세스 정책 배포](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>모니터

이 예제에서는 시간 경과에 따른 조건부 액세스 정책 변경 내용을 모니터링하고 키 정책이 변경될 때 경고를 트리거할 수 있는 메커니즘을 제공합니다.

- [배포된 조건부 액세스 정책의 변경 내용 모니터링 및 경고 트리거](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>관리

### <a name="backup-and-restore"></a>백업 및 복원

이 예제를 사용하여 Teams에서 승인을 받아 조건부 액세스 정책의 백업 및 복원을 자동화합니다.

- [Microsoft Graph API 호출을 사용하여 조건부 액세스 정책의 백업 및 복원 프로세스 관리](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>응급 액세스 계정

여러 관리자가 조건부 액세스 정책을 만들 수 있으며 해당 정책에 대한 제외로 [응급 액세스 계정](../roles/security-emergency-access.md)을 추가하지 않을 수 있습니다. 이 예제에서는 지정된 응급 액세스 계정을 포함하도록 모든 정책을 업데이트합니다.

- [Microsoft Graph API 호출을 사용하여 조건부 액세스 정책에 대한 응급 액세스 계정 할당 관리](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>대체 계획

작업이 항상 원하는 방식으로 작동하지는 않습니다. 이 경우 작업을 계속할 수 있는 상태로 돌아갈 방법이 필요합니다. 다음 예제에서는 정책을 알려진 좋은 대체 계획으로 되돌리고 다른 조건부 액세스 정책을 사용하지 않는 방법을 제공합니다.

- [Microsoft Graph API 호출을 사용하여 조건부 액세스 대체 정책 활성화 관리](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>커뮤니티 기여

관련 샘플은 [GitHub 리포지토리](https://github.com/Azure-Samples/azure-ad-conditional-access-apis)에 있습니다. Microsoft는 GitHub 이슈와 끌어오기 요청을 통한 커뮤니티 기여를 적극적으로 지원해 드리겠습니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Graph 개요](/graph/overview)

- [조건부 액세스 API](/graph/api/resources/conditionalaccesspolicy)

- [명명된 위치 API](/graph/api/resources/namedlocation)
