---
title: Azure API Management의 수정 버전 | Microsoft Docs
description: Azure API Management의 수정 버전 개념에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: 16a5dc58a8fddca2cef2c393aaecca61fef61a3c
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187724"
---
# <a name="revisions-in-azure-api-management"></a>Azure API Management의 수정 버전

수정 버전을 통해 안전한 제어 방식으로 API를 변경할 수 있습니다. 변경하려면 새 수정 버전을 만듭니다. 그런 다음 API 소비자를 방해하지 않고 API를 편집 및 테스트할 수 있습니다. 준비가 되면 수정 버전을 현재 수정 버전으로 설정합니다. 동시에 선택적으로 변경 로그에 항목을 게시하여 API 소비자를 변경된 내용의 최신 상태로 유지할 수 있습니다. 변경 로그는 개발자 포털에 게시됩니다.

> [!NOTE]
> 개발자 포털은 소비 계층에서 사용할 수 없습니다.

수정 버전으로 다음을 수행할 수 있습니다.

- 프로덕션 API를 방해하지 않고 API 정의 및 정책을 안전하게 변경합니다.
- 게시하기 전에 변경 내용을 사용해 보세요.
- 개발자가 새로운 기능을 이해할 수 있도록 변경 내용을 문서화합니다.
- 문제가 발견되면 롤백합니다.

[연습을 수행하여 수정 버전을 시작하세요.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>특정 수정 버전 액세스

API에 대한 각 수정 버전에는 특별한 형식의 URL을 사용하여 액세스할 수 있습니다. API URL의 끝에(쿼리 문자열 앞에) `;rev={revisionNumber}`를 추가하여 해당 API의 특정 수정 버전에 액세스합니다. 예를 들어 다음 URL을 사용하여 `customers` API의 수정 버전 3에 액세스할 수 있습니다.

`https://apis.contoso.com/customers;rev=3?customerId=123`

기본적으로 각 수정 버전에는 현재 수정 버전과 동일한 보안 설정이 있습니다. 각 수정 버전에 대해 다른 보안을 적용하려는 경우 특정 수정 버전에 대한 정책을 의도적으로 변경할 수 있습니다. 예를 들어 외부 호출자가 아직 개발 중인 수정 버전에 액세스하지 못하도록 [IP 필터링 정책](./api-management-access-restriction-policies.md#RestrictCallerIPs)을 추가할 수 있습니다.

수정 버전을 오프라인 상태로 만들 수 있습니다. 이 경우 해당 URL을 통해 수정 버전에 액세스하려고 하는 경우에도 호출자가 액세스할 수 없게 됩니다. Azure Portal을 사용하여 수정 버전을 오프라인으로 표시할 수 있습니다. PowerShell을 사용하는 경우 `Set-AzApiManagementApiRevision` cmdlet을 사용하여 `Path` 인수를 `$null`로 설정할 수 있습니다.

> [!NOTE]
> 테스트에 사용하지 않는 경우 수정 버전을 오프라인으로 설정하는 것이 좋습니다.

## <a name="current-revision"></a>현재 수정 버전

단일 수정 버전을 *현재* 수정 버전으로 설정할 수 있습니다. 이 수정 버전은 URL에 명시적 수정 버전 번호를 지정하지 않는 모든 API 요청에 사용되는 버전입니다. 이전 수정 버전을 현재 버전으로 설정하여 해당 수정 버전으로 롤백할 수 있습니다.

Azure Portal을 사용하여 수정 버전을 현재 수정 버전으로 설정할 수 있습니다. PowerShell을 사용하는 경우 `New-AzApiManagementApiRelease` cmdlet을 사용할 수 있습니다.

## <a name="revision-descriptions"></a>수정 버전 설명

수정 버전을 만들 때 고유의 추적 용도에 대한 설명을 설정할 수 있습니다. 설명은 API 사용자에게 표시되지 않습니다.

수정 버전을 현재로 설정하는 경우 경우에 따라 퍼블릭 변경 로그 정보도 지정할 수 있습니다. 변경 로그는 API 사용자가 볼 수 있도록 개발자 포털에 포함됩니다. `Update-AzApiManagementApiRelease` PowerShell cmdlet을 사용하여 변경 로그 정보를 수정할 수 있습니다.

> [!NOTE]
> **표시 이름** 및 **API 접미사** 와 같은 특정 API 속성은 현재 수정 버전에서만 업데이트할 수 있습니다.

## <a name="versions-and-revisions"></a>버전 및 수정 버전

버전 및 수정 버전은 별개의 기능입니다. 각 버전에는 버전이 없는 API와 마찬가지로 여러 개의 수정 버전이 있을 수 있습니다. 버전을 사용하지 않고 수정 버전을 사용하거나 다른 방법을 사용할 수 있습니다. 일반적으로 버전은 API 버전을 주요 변경 내용과 구분하는 데 사용되고, 수정 버전은 API에 대한 사소한 변경 내용에 사용할 수 있습니다.

수정 버전에 호환성이 손상되는 변경이 포함되어 있거나, 정식으로 수정 버전을 베타/테스트 버전으로 전환하려는 경우 수정 버전에서 버전을 만들 수 있습니다. Azure Portal을 사용하여 수정 버전 탭의 수정 버전 상황에 맞는 메뉴에서 '수정 버전에서 버전 만들기'를 클릭합니다.
