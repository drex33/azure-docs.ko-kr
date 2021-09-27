---
title: 개발자 포털 배포 자동화
titleSuffix: Azure API Management
description: 자체 호스팅 개발자 포털 콘텐츠를 두 API Management 서비스 간에 자동으로 마이그레이션하는 방법을 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: a2fab946179fb0bef9db0068bb8f660f91ca8c04
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669154"
---
# <a name="automate-developer-portal-deployments"></a>개발자 포털 배포 자동화

API Management 개발자 포털은 콘텐츠에 대한 프로그래밍 방식 액세스를 지원합니다. [콘텐츠 관리 REST API](/rest/api/apimanagement/)를 통해 API Management 서비스로 데이터를 가져오거나 API Management 서비스로 데이터를 내보낼 수 있습니다. REST API 액세스는 관리형 포털과 자체 호스팅 포털 양쪽에 대해 사용할 수 있습니다.

## <a name="automated-migration-script"></a>자동 마이그레이션 스크립트

API를 사용하여 두 API Management 서비스(예: 테스트 환경에 있는 서비스와 프로덕션 환경에 있는 서비스) 간에 콘텐츠 마이그레이션을 자동화할 수 있습니다. API Management 개발자 포털 [GitHub 리포지토리](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js)에 있는 `scripts.v3/migrate.js` 스크립트를 사용하면 자동화 프로세스가 간소화됩니다.

> [!WARNING]
> 스크립트는 대상 API Management 서비스에서 개발자 포털의 콘텐츠를 제거합니다. 이 작업이 우려가 된다면 백업을 만들어 두세요.

> [!NOTE]
> 외부적으로 정의된 사용자 지정 스토리지 계정을 갖는 자체 호스팅 포털을 사용하여 미디어 파일을 호스팅하는 경우(즉, `config.design.json` 구성 파일의 `blobStorageUrl` 설정을 정의한 경우) 원본 `scripts/migrate.js` [스크립트](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js)를 사용해야 합니다. 원본 스크립트는 관리형 포털이나 API Management에 의해 관리되는 미디어 스토리지 계정을 갖는 자체 호스팅 포털에 대해서는 작동하지 않습니다. 이 경우에는 대신 `/scripts.v3` 폴더에 있는 스크립트를 사용하세요.

스크립트에서 수행하는 단계는 다음과 같습니다.

1. 원본 API Management 서비스에서 포털 콘텐츠 및 미디어를 캡처합니다.
1. 대상 API Management 서비스에서 포털 콘텐츠 및 미디어를 제거합니다.
1. 대상 API Management 서비스로 포털 콘텐츠 및 미디어를 업로드합니다.
1. 선택적으로, 관리형 포털에 대해서만 자동으로 포털을 게시합니다.

스크립트가 성공적으로 실행되면 대상 API Management 서비스에 원본 서비스와 동일한 포털 콘텐츠가 있게 되며 해당 콘텐츠를 관리자 권한으로 볼 수 있습니다.

* 관리형 포털을 사용하는 경우 스크립트가 대상 포털을 자동으로 게시하도록 설정하여 방문자가 자동으로 마이그레이션된 버전을 사용할 수 있도록 할 수 있습니다. 
* 자체 호스팅 포털을 사용하는 경우 대상 포털을 수동으로 게시해야 합니다. 자습서의 게시 및 호스팅 지침에 따라 [자체 호스팅 개발자 포털을 설정](developer-portal-self-host.md)하세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 통해 개발자 포털에 대해 자세히 알아봅니다.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)
- [Self-host the developer portal](developer-portal-self-host.md)(개발자 포털 자체 호스팅)