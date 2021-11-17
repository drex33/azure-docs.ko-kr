---
title: Functions에 대한 Azure SQL 바인딩
description: Azure Functions Azure SQL 바인딩을 사용하는 방법을 이해합니다.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: ece79028f5cf0211f7d7345d54e1ccdb096cc6c9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490746"
---
# <a name="azure-sql-bindings-for-azure-functions-overview-preview"></a>Azure Functions 개요(미리 보기)에 대한 Azure SQL 바인딩

이 문서 집합에서는 [Azure Functions Azure SQL](/azure/azure-sql/) 바인딩을 작업하는 방법을 설명합니다. Azure Functions Azure SQL 및 SQL Server 제품에 대한 입력 및 출력 바인딩을 지원합니다.

| 작업 | 형식 |
|---------|---------|
| 데이터베이스에서 데이터 읽기 | [입력 바인딩](./functions-bindings-azure-sql-input.md) |
| 데이터베이스에 데이터 저장 |[출력 바인딩](./functions-bindings-azure-sql-output.md) |

> [!NOTE]
> 이 참조는 [Azure Functions 버전 2.x 이상](functions-versions.md)에 대한 것입니다. 
>
> 이 바인딩을 사용하려면 Azure SQL 또는 SQL Server 데이터베이스에 연결해야 합니다.

## <a name="add-to-your-functions-app"></a>Functions 앱 추가

### <a name="functions"></a>Functions

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [미리 보기 NuGet 패키지] 설치 | |
<!--| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. | -->


[미리 보기 NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Sql
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack


## <a name="known-issues"></a>알려진 문제

- , 또는 데이터 형식의 열이 있는 테이블에 대한 출력 `NTEXT` `TEXT` `IMAGE` 바인딩은 지원되지 않으며 데이터 upsert가 실패합니다. 이러한 [형식은](https://docs.microsoft.com/sql/t-sql/data-types/ntext-text-and-image-transact-sql) 이후 버전의 SQL Server 제거되며 이 Azure Functions 바인딩에서 사용하는 함수와 호환되지 `OPENJSON` 않습니다.
- 대/소문자 구분 [데이터 정렬은](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#Collation_Defn) 현재 지원되지 않습니다. [GitHub 항목 #133은](https://github.com/Azure/azure-functions-sql-extension/issues/133) 이 문제에 대한 진행 상황을 추적합니다.


## <a name="open-source"></a>오픈 소스

Azure Functions 대한 Azure SQL 바인딩은 오픈 소스이며 의 리포지토리에서 사용할 수 [https://github.com/Azure/azure-functions-sql-extension](https://github.com/Azure/azure-functions-sql-extension) 있습니다.


## <a name="next-steps"></a>다음 단계

- [데이터베이스에서 데이터 읽기(입력 바인딩)](./functions-bindings-azure-sql-input.md)
- [데이터베이스에 데이터 저장(출력 바인딩)](./functions-bindings-azure-sql-output.md)