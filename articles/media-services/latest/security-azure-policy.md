---
title: Media Services의 Azure Policy 기본 제공 지원
description: 이 문서에서는 Azure Media Services 시나리오에 대한 Azure Policy 기본 제공 지원에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 88b1907d0c767d77dd4a1f2e38ab859d4147ea96
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122530986"
---
# <a name="azure-policy-for-media-services"></a>Media Services에 대한 Azure Policy

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services는 조직의 표준 및 규정 준수를 대규모로 적용하는 데 도움이 되는 몇 가지 기본 제공 [Azure Policy](../../governance/policy/overview.md) 정의를 제공합니다.
Azure Policy에 대한 일반적인 사용 사례에는 리소스 일관성, 규정 준수, 보안, 비용 및 관리에 대한 거버넌스 구현이 포함됩니다.

Media Services는 시작하는 데 도움이 되는 기본 제공 Azure Policy에 대한 몇 가지 일반적인 사용 사례 정의를 제공합니다.

## <a name="built-in-azure-policy-definitions-for-media-services"></a>Media Services에 대한 기본 제공 Azure Policy 정의

몇 가지 기본 제공 정책 사용 사례 정의는 Media Services와 사용하여 시작하는 데 도움이 되며 사용자 고유의 사용자 지정 정책을 정의할 수 있습니다.

[!INCLUDE [Azure Policy Media Services](../../../includes/policy/reference/bycat/policies-media-services.md)]

[Media Services에 대한 기본 제공 정책 정의 목록](../../governance/policy/samples/built-in-policies.md#media-services)은 최신 정의를 제공하고 코드 정의와 포털에서 액세스하는 방법을 연결합니다.

## <a name="common-scenarios-that-require-azure-policy"></a>Azure Policy가 필요한 일반적인 시나리오

* 엔터프라이즈 보안에서 모든 Media Services 계정이 Private Link를 사용하여 생성되도록 해야 하는 경우 정책 정의를 사용하여 계정이 2020-05-01 API(이상)로만 만들어지도록 하여 레거시 REST v2 API에 대한 액세스를 사용하지 않고 Private Link 기능에 액세스할 수 있습니다.
* 콘텐츠 키 정책에 사용되는 토큰에 특정 옵션을 적용하려는 경우 특정 요구 사항을 지원하기 위해 Azure Policy 정의를 생성할 수 있습니다.
* 보안 목표를 위해 작업 입력 원본을 신뢰할 수 있는 스토리지 계정에서만 가져오도록 제한하고 JobInputHttp를 사용하여 외부 HTTP(S) 입력에 대한 액세스를 제한해야 하는 경우 Azure Policy를 생성하여 입력 URI 패턴을 제한할 수 있습니다.

## <a name="example-policy-definitions"></a>예제 정책 정의

Azure Media Services는 Git 허브에서 샘플 Azure Policy 정의 집합을 유지 관리하고 게시합니다.
azure-policy Git 허브 리포지토리에서 [Media Services에 대한 기본 제공 정책 정의](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policyDefinitions/Media%20Services) 샘플을 참조하세요.

자세한 내용은 다음 문서를 참조하세요.

- [Azure Policy란](../../governance/policy/overview.md)
- [빠른 시작: 포털에서 정책 만들기](../../governance/policy/assign-policy-portal.md)
- [Media Services에 대한 기본 제공 정책 정의 목록](../../governance/policy/samples/built-in-policies.md#media-services)

## <a name="next-steps"></a>다음 단계

- [Media Services v3 API로 개발](media-services-apis-overview.md)
- [Media Services의 역할 기반 액세스 제어](security-rbac-concept.md)
- [Media Services를 사용하여 프라이빗 링크 방법](security-private-link-how-to.md)