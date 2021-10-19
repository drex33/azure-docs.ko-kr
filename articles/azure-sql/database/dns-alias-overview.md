---
title: DNS 별칭
description: 애플리케이션은 Azure SQL Database용 서버 이름의 별칭에 연결할 수 있습니다. 한편, 별칭이 가리키는 SQL Database를 언제든지 변경하여 테스트 등의 작업을 용이하게 수행할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 64aaf922229f97856a888d84e91be670f74faa1d
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166256"
---
# <a name="dns-alias-for-azure-sql-database"></a>Azure SQL Database의 DNS 별칭
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database에는 DNS(Domain Name System) 서버가 있습니다. PowerShell 및 REST API는 [논리 SQL 서버](logical-servers.md) 이름에 대한 [DNS 별칭을 만들고 관리하기 위한 호출](#anchor-powershell-code-62x)을 수락합니다.

서버 이름 대신 *DNS 별칭* 을 사용할 수 있습니다. 클라이언트 프로그램은 연결 문자열에 이 별칭을 사용할 수 있습니다. DNS 별칭은 클라이언트 프로그램을 서로 다른 서버로 리디렉션할 수 있는 변환 계층을 제공합니다. 이 계층은 모든 클라이언트와 해당 연결 문자열을 찾아서 편집해야 하는 어려움을 덜어줍니다.

DNS 별칭의 일반적인 용도에는 다음과 같은 경우가 포함됩니다.

- 서버에 대해 기억하기 쉬운 이름을 만듭니다.
- 초기 개발 중에 별칭은 테스트 서버를 참조할 수 있습니다. 애플리케이션이 라이브되면 프로덕션 서버를 참조하도록 별칭을 수정할 수 있습니다. 테스트에서 프로덕션 환경으로 전환하기 위해 서버에 연결되는 여러 클라이언트의 구성을 수정할 필요는 없습니다.
- 애플리케이션의 데이터베이스만 다른 서버로 이동된다고 가정합니다. 여러 클라이언트 구성을 수정하지 않고도 별칭을 수정할 수 있습니다.
- 지역 가동 중단 중에는 지역 복원을 사용하여 다른 서버 및 지역에서 데이터베이스를 복구합니다. 기존 클라이언트 애플리케이션이 다시 연결될 수 있게 새 서버를 가리키도록 기존 별칭을 수정할 수 있습니다.

## <a name="domain-name-system-dns-of-the-internet"></a>인터넷의 DNS(Domain Name System)

인터넷은 DNS에 의존합니다. DNS는 친숙한 이름을 서버의 이름으로 변환합니다.

## <a name="scenarios-with-one-dns-alias"></a>DNS 별칭을 하나 사용하는 시나리오

시스템을 새 서버로 전환해야 한다고 가정합니다. 이전에는 모든 클라이언트 프로그램에서 모든 연결 문자열을 찾아서 업데이트해야 했습니다. 하지만 이제 연결 문자열에서 DNS 별칭을 사용하는 경우 별칭 속성을 업데이트해야 합니다.

Azure SQL Database의 DNS 별칭 기능은 다음과 같은 시나리오에서 도움이 됩니다.

### <a name="test-to-production"></a>테스트 ~ 프로덕션 환경

클라이언트 프로그램 개발을 시작할 때 사용자가 연결 문자열에 DNS 별칭을 사용하도록 합니다. 별칭의 속성이 서버의 테스트 버전을 가리키도록 합니다.

나중에 새 시스템이 프로덕션 환경으로 전환되면 별칭의 속성이 서버를 가리키도록 업데이트할 수 있습니다. 클라이언트 프로그램을 변경할 필요는 없습니다.

### <a name="cross-region-support"></a>지역 간 지원

재해 복구 시 서버가 다른 지리적 지역으로 이동될 수 있습니다. DNS 별칭을 사용하던 시스템의 경우, 모든 클라이언트에 대한 모든 연결 문자열을 찾아서 업데이트할 필요는 없습니다. 대신, 이제 Azure SQL Database 호스트하는 새 서버를 참조하도록 별칭을 업데이트할 수 있습니다.

## <a name="properties-of-a-dns-alias"></a>DNS 별칭의 속성

다음 속성은 서버에 대한 각 DNS 별칭에 적용됩니다.

- *고유 이름:* 사용자가 만드는 각 별칭 이름은 서버 이름과 마찬가지로 모든 서버에서 고유합니다.
- *서버 필수:* 정확히 한 서버를 참조하지 않는 한, DNS 별칭을 만들 수 없으며, 서버가 미리 존재해야 합니다. 업데이트된 별칭은 항상 정확히 하나의 기존 서버를 참조해야 합니다.
  - 서버를 삭제하면 Azure 시스템에서도 해당 서버를 참조하는 모든 DNS 별칭을 삭제합니다.
- *어떤 지역에도 바인딩되지 않음:* DNS 별칭은 지역에 바인딩되지 않습니다. 모든 DNS 별칭을 업데이트하여 지리적 지역에 있는 서버를 참조할 수 있습니다.
  - 그러나 다른 서버를 참조하도록 별칭을 업데이트할 경우 두 서버 모두 동일한 Azure *구독* 에 있어야 합니다.
- *사용 권한:* DNS 별칭을 관리하려면 *서버 참가자* 권한 이상이 있어야 합니다. 자세한 내용은 [Azure Portal에서 Azure 역할 기반 액세스 제어 시작](../../role-based-access-control/overview.md)을 참조하세요.

## <a name="manage-your-dns-aliases"></a>DNS 별칭 관리

REST API와 PowerShell cmdlet을 사용하면 DNS 별칭을 프로그래밍 방식으로 관리할 수 있습니다.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>DNS 별칭을 관리하기 위한 REST API

REST API에 대한 설명서는 다음 웹 위치 근처에서 사용할 수 있습니다.

- [Azure SQL Database REST API](/rest/api/sql/)

또한 REST API는 다음의 GitHub에서 볼 수 있습니다.

- [Azure SQL Database DNS 별칭 REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>DNS 별칭을 관리하기 위한 PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 지원되지만 향후 모든 개발은 Az.Sql 모듈에 집중됩니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

REST API를 호출하는 PowerShell cmdlet을 사용할 수 있습니다.

DNS 별칭을 관리하는 데 사용되는 PowerShell cmdlet의 코드 예제는 다음에 설명되어 있습니다.

- [Azure SQL Database의 DNS 별칭에 대한 PowerShell](dns-alias-powershell-create.md)

코드 예제에 사용된 cmdlet은 다음과 같습니다.

- [New-AzSqlServerDnsAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Azure SQL Database 서비스 시스템에 새 DNS 별칭을 만듭니다. 별칭은 서버 1을 참조합니다.
- [Get-AzSqlServerDnsAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): 서버 1에 할당된 모든 DNS 별칭을 가져와서 나열합니다.
- [Set-AzSqlServerDnsAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): 별칭이 참조하도록 구성된 서버 이름을 서버 1에서 서버 2로 수정합니다.
- [Remove-AzSqlServerDnsAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): 별칭의 이름을 사용하여 서버 2에서 DNS 별칭을 제거합니다.

## <a name="limitations"></a>제한 사항

현재, DNS 별칭에는 다음과 같은 제한이 있습니다.

- *최대 2분 간 지연:* DNS 별칭을 업데이트하거나 제거하는 데는 최대 2분이 소요됩니다.
  - 지연 시간이 길어지든, 짧아지든, 별칭은 레거시 서버에 대한 클라이언트 연결 참조를 즉시 중지합니다.
- *DNS 조회:* 현재, DNS 별칭이 지정된 서버를 확인하는 신뢰할 수 있는 유일한 방법은 [DNS 조회](/windows-server/administration/windows-commands/nslookup)를 수행하는 것입니다.
- _테이블 감사는 지원되지 않음_: 데이터베이스에 대해 *테이블 감사* 가 사용되도록 설정된 서버에서는 DNS 별칭을 사용할 수 없습니다.
  - 테이블 감사는 더 이상 사용되지 않습니다.
  - 따라서 [Blob 감사](../../azure-sql/database/auditing-overview.md)로 전환하는 것이 좋습니다.

## <a name="related-resources"></a>관련 참고 자료

- 재해 복구를 포함하는 [Azure SQL Database의 비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- [Azure REST API 참조](/rest/api/azure/)
- [서버 Dns 별칭 API](/rest/api/sql/2020-11-01-preview/serverdnsaliases)

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database의 DNS 별칭에 대한 PowerShell](dns-alias-powershell-create.md)
