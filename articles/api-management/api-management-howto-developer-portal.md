---
title: Azure API Management에서 개발자 포털 개요
titleSuffix: Azure API Management
description: API 소비자가 API를 탐색할 수 있는 사용자 지정 가능한 웹 사이트인 API Management의 개발자 포털에 대해 알아봅니다.
services: api-management
documentationcenter: API Management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4fa9b0e777ce64322ebad74c8279724817530790
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664583"
---
# <a name="overview-of-the-developer-portal"></a>개발자 포털 개요

개발자 포털은 API 설명서를 사용하여 원하는 대로 사용자 지정 가능한 자동 생성 웹 사이트입니다. API 소비자는 API를 검색하고, API 사용 방법을 배우고 액세스를 요청하고 사용해 볼 수 있습니다.

이 문서에서 설명하는 것처럼 특정 시나리오에 맞게 개발자 포털을 사용자 지정하고 확장할 수 있습니다. 

![API Management 개발자 포털](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>레거시 포털에서 마이그레이션

> [!IMPORTANT]
> 이전 개발자 포털은 더 이상 사용되지 않으며 보안 업데이트만 수신합니다. 모든 API Management 서비스에서 제거되는 2023년 10월 만료될 때까지 평상시와 같이 계속 사용할 수 있습니다.

새 개발자 포털로 마이그레이션하는 방법은 [전용 설명서 문서](developer-portal-deprecated-migration.md)에 설명되어 있습니다.

## <a name="customization-and-styling"></a>사용자 지정 및 스타일 지정

개발자 포털은 기본 제공되는 끌어서 놓기 비주얼 편집기를 통해 사용자 지정하고 스타일을 지정할 수 있습니다. 자세한 내용은 [이 자습서](api-management-howto-developer-portal-customize.md)를 참조하세요.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> 확장성

API Management 서비스에는 기본 제공되고 항상 최신의 **관리되는** 개발자 포털이 포함됩니다. Azure Portal 인터페이스에서 액세스할 수 있습니다.

기본으로 지원되지 않는 사용자 지정 논리를 사용하여 확장해야 하는 경우 해당 코드 베이스를 수정할 수 있습니다. 포털의 코드 베이스는 [GitHub 리포지토리에서 사용할 수](https://github.com/Azure/api-management-developer-portal)있습니다. 예를 들어 타사 지원 시스템과 통합되는 새 위젯을 구현할 수 있습니다. 새로운 기능을 구현할 때 다음의 옵션 중에서 하나를 선택할 수 있습니다.

- API Management 서비스 외부에서 결과 포털을 **자체 호스트** 합니다. 포털을 자체 호스트하는 경우 사용자는 유지 관리자가 되며 업그레이드를 담당하게 됩니다. Azure 지원의 지원은 [자체 호스팅 포털의 기본 설정](developer-portal-self-host.md)으로만 제한됩니다.
- API Management 팀에 대한 끌어오기 요청을 열어 새 기능을 **관리되는** 포털의 코드베이스에 통합합니다.

확장성 세부 정보 및 지침에 대해서는 [GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal) 및 자습서를 참조하여 [위젯을 구현](developer-portal-implement-widgets.md)합니다. [관리되는 포털을 사용자 지정하는](api-management-howto-developer-portal-customize.md) 자습서는 포털의 관리 패널을 안내하며, 이는 **관리되는** 및 **자체 호스트** 버전에 공통적입니다.


## <a name="next-steps"></a>다음 단계

새 개발자 포털에 대해 자세히 알아봅니다.

- [관리형 개발자 포털 액세스 및 사용자 지정](api-management-howto-developer-portal-customize.md)
- [포털의 자체 호스트 버전 설정](developer-portal-self-host.md)
- [고유한 위젯 구현](developer-portal-implement-widgets.md)

다른 리소스 찾아보기:

- [소스 코드를 포함한 GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal)
- [개발자 포털에 대한 질문과 대답](developer-portal-faq.md)
