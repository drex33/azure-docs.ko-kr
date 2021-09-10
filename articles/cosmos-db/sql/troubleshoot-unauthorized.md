---
title: Azure Cosmos DB 권한 없는 예외 문제 해결
description: 권한 없는 예외를 진단하고 해결하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f0a42b376f9a7736056c712b9a631f0cabc1f575
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117195"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Azure Cosmos DB 권한 없는 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 401: HTTP 요청에 있는 MAC 서명이 계산된 서명과 동일하지 않습니다.
401 오류 메시지 "HTTP 요청에 있는 MAC 서명이 계산된 서명과 동일하지 않음"이 표시되는 경우, 이 문제는 다음과 같은 시나리오에서 발생할 수 있습니다.

이전 SDK의 경우 예외가 올바른 401 권한 없는 예외 대신 잘못된 JSON 예외로 표시될 수 있습니다. 최신 SDK는 이 시나리오를 올바르게 처리하고 올바른 오류 메시지를 제공합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 인증되지 않은 예외에 대한 알려진 원인과 해결책이 나와 있습니다.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>가장 일반적인 시나리오는 키가 제대로 회전되지 않은 것입니다.
401 MAC 서명은 키 회전 직후에 표시되며 결국에는 변경하지 않고 중지됩니다. 

#### <a name="solution"></a>해결책:
키가 회전되고 [모범 사례](../secure-access-to-data.md#key-rotation)를 따르지 않았습니다. Azure Cosmos DB 계정 키 회전은 Azure Cosmos DB 계정 크기에 따라 몇 초에서 며칠까지 걸릴 수 있습니다.

### <a name="the-key-is-misconfigured"></a>키가 잘못 구성되었습니다. 
401 MAC 서명 문제가 해당 키를 사용하는 모든 호출에 대해 일관되게 발생합니다.

#### <a name="solution"></a>해결책:
애플리케이션에서 키가 잘못 구성되었으며 계정에 대해 잘못된 키를 사용하고 있거나 전체 키가 복사되지 않았습니다.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>애플리케이션이 쓰기 작업에 읽기 전용 키를 사용하고 있습니다.
401 MAC 서명 문제는 만들기 또는 바꾸기와 같은 쓰기 작업에서만 발생하고 읽기 요청은 성공합니다.

#### <a name="solution"></a>해결책:
애플리케이션에서 읽기/쓰기 키를 사용하도록 전환하여 작업을 성공적으로 완료할 수 있도록 합니다.

### <a name="race-condition-with-create-container"></a>컨테이너 만들기와 경합 상태
401 MAC 서명 문제는 컨테이너 만들기 직후에 나타납니다. 이 문제는 컨테이너 만들기가 완료될 때까지만 발생합니다.

#### <a name="solution"></a>해결책:
컨테이너 만들기와 경합 상태가 발생합니다. 컨테이너 만들기가 완료되기 전에 애플리케이션 인스턴스가 컨테이너에 액세스하려고 합니다. 애플리케이션이 실행되는 동안 컨테이너가 삭제되고 같은 이름으로 다시 만들어지는 경우가 이 경합 상태에 대한 가장 일반적인 시나리오입니다. SDK는 새 컨테이너를 사용하려고 하지만 컨테이너 만들기가 아직 진행 중이므로 키를 포함하지 않습니다.

### <a name="bulk-mode-enabled"></a>벌크 모드 사용 
[벌크 모드 사용](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/)을 설정하면 읽기 및 쓰기 작업이 최적의 네트워크 성능을 위해 최적화되고 전용 대량 API를 통해 백 엔드로 전송됩니다. 벌크 모드를 사용하는 상태에서 읽기 작업을 수행하는 동안 401 오류가 발생하면 애플리케이션이 [읽기 전용 키](../secure-access-to-data.md#primary-keys)를 사용하고 있음을 의미하는 경우가 많습니다.

#### <a name="solution"></a>솔루션
벌크 모드를 사용하는 상태에서 작업을 수행할 때 읽기/쓰기 키 또는 쓰기 권한 부여 메커니즘을 사용합니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용하고 있는 경우 문제를 [진단 및 해결](troubleshoot-dot-net-sdk.md)합니다.
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.
* Azure Cosmos DB Java v4 SDK를 사용할 때 발생하는 문제를 [진단하고 해결](troubleshoot-java-sdk-v4-sql.md)합니다.
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)의 성능 지침에 대해 알아봅니다.