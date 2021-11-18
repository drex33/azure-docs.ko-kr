---
title: 함수에 대 한 Azure SQL 바인딩
description: Azure Functions에서 Azure SQL 바인딩을 사용 하는 방법을 이해 합니다.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: b5054da620e67c38b427c12956d73727bf23d519
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715381"
---
# <a name="azure-sql-bindings-for-azure-functions-overview-preview"></a>Azure Functions 개요 (미리 보기)에 대 한 Azure SQL 바인딩

이 문서 집합에서는 Azure Functions에서 [Azure SQL](../azure-sql/index.yml) 바인딩을 사용 하는 방법을 설명 합니다. Azure Functions는 Azure SQL 및 SQL Server 제품에 대 한 입력 및 출력 바인딩을 지원 합니다.

| 작업 | 형식 |
|---------|---------|
| 데이터베이스에서 데이터 읽기 | [입력 바인딩](./functions-bindings-azure-sql-input.md) |
| 데이터베이스에 데이터 저장 |[출력 바인딩](./functions-bindings-azure-sql-output.md) |

> [!NOTE]
> 이 참조는 [Azure Functions 버전 2.x 이상](functions-versions.md)에 대한 것입니다. 
>
> 이 바인딩을 사용 하려면 Azure SQL 또는 SQL Server 데이터베이스에 연결 해야 합니다.

## <a name="add-to-your-functions-app"></a>Functions 앱 추가

### <a name="functions"></a>함수

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [미리 보기 NuGet 패키지] 설치 | |
<!--| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. | -->


[NuGet 패키지 미리 보기]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Sql
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack


## <a name="known-issues"></a>알려진 문제

- 데이터 형식이, 또는 인 열이 있는 테이블에 대 한 출력 바인딩은 `NTEXT` `TEXT` `IMAGE` 지원 되지 않으며 데이터 upsert가 실패 합니다. 이러한 형식은 SQL Server의 이후 버전에서 [제거](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) 되며 `OPENJSON` 이 Azure Functions 바인딩에서 사용 하는 함수와 호환 되지 않습니다.
- 대/소문자 구분 [데이터 정렬은](/sql/relational-databases/collations/collation-and-unicode-support#Collation_Defn) 현재 지원 되지 않습니다. [GitHub 항목 #133](https://github.com/Azure/azure-functions-sql-extension/issues/133) 이 문제에 대 한 진행률을 추적 합니다.


## <a name="open-source"></a>오픈 소스

Azure Functions에 대 한 Azure SQL 바인딩은 오픈 소스 이며의 리포지토리에서 사용할 수 있습니다 [https://github.com/Azure/azure-functions-sql-extension](https://github.com/Azure/azure-functions-sql-extension) .


## <a name="next-steps"></a>다음 단계

- [데이터베이스에서 데이터 읽기 (입력 바인딩)](./functions-bindings-azure-sql-input.md)
- [데이터베이스에 데이터 저장 (출력 바인딩)](./functions-bindings-azure-sql-output.md)