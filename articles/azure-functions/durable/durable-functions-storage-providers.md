---
title: Durable Functions 스토리지 공급자 - Azure
description: 다양한 Durable Functions 스토리지 공급자 및 비교 방법에 대해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: azfuncdf
ms.openlocfilehash: bbeb730b0e22bd555f06514870448a229fccfbc6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438104"
---
# <a name="durable-functions-storage-providers"></a>Durable Functions 스토리지 공급자

Durable Functions는 안정적인 실행을 보장하기 위해 함수 매개 변수, 반환 값 및 기타 상태를 지속성 스토리지에 자동으로 유지합니다. Durable Functions의 기본 구성은 이 런타임 상태를 Azure Storage(클래식) 계정에 저장합니다. 그러나 대체 지속성 스토리지 공급자를 사용하도록 Durable Functions v2.0 이상을 구성할 수 있습니다.

Durable Functions는 내부적으로 [Durable Task Framework](https://github.com/Azure/durabletask)(DTFx)로 구동하는 Azure Functions 트리거 및 바인딩 세트입니다. DTFx는 Durable Functions에서 사용하는 Azure Storage 공급자를 포함하여 다양한 백 엔드 스토리지 공급자를 지원합니다. Durable Functions **v2.5.0** 부터 사용자는 Azure Storage 공급자 대신 DTFx 스토리지 공급자를 사용하도록 함수 앱을 구성할 수 있습니다.

> [!NOTE]
> Azure Storage 대신 다른 스토리지 공급자를 사용하도록 선택할 때에는 신중해야 합니다. Azure에서 실행되는 대부분의 함수 앱은 기본 Azure Storage 공급자를 Durable Functions에 사용해야 합니다. 그러나 대체 스토리지 공급자를 사용할지 여부를 결정할 때 고려해야 하는 중요한 비용, 확장성 및 데이터 관리 측면의 득과 실이 있습니다. 이 문서에서는 이러한 득실에 대해 자세히 설명합니다.
>
> 또한 현재 한 스토리지 공급자에서 다른 스토리지 공급자로 데이터를 마이그레이션할 수 없습니다. 새 스토리지 공급자를 사용하려면 새 스토리지 공급자로 구성된 새 앱을 만들어야 합니다.

두 가지 대체 DTFx 스토리지 공급자는 Durable Functions, _Netherite_ 스토리지 공급자 및 _MSSQL(Microsoft SQL Server)_ 스토리지 공급자와 함께 사용하도록 개발되었습니다. 이 문서에서는 지원되는 세 가지 공급자를 모두 설명하고, 서로 비교하고, 시작하는 방법에 대한 기본 정보를 제공합니다.

## <a name="azure-storage"></a>Azure Storage

Azure Storage는 Durable Functions의 기본 스토리지 공급자입니다. 큐, 테이블 및 Blob을 사용하여 오케스트레이션 및 엔터티 상태를 유지합니다. 또한 Blob 및 Blob 임대를 사용하여 파티션을 관리합니다. 대부분의 경우 Durable Functions 런타임 상태를 저장하는 데 사용되는 스토리지 계정은 Azure Functions에서 사용하는 기본 스토리지 계정(`AzureWebJobsStorage`)과 동일합니다. 그러나 별도의 스토리지 계정으로 Durable Functions를 구성할 수도 있습니다. Azure Storage 공급자는 Durable Functions 확장에 기본 제공되며 다른 종속성이 없습니다.

Azure Storage 공급자의 주요 이점은 다음과 같습니다.

* 설정이 필요 없음 - 함수 앱 설정 환경에서 자동으로 생성하는 스토리지 계정을 사용할 수 있습니다.
* 저렴한 서버리스 청구 모델 - Azure Storage는 전적으로 사용량을 기반으로 하는 사용량 기반 가격 책정 모델입니다([자세한 정보](durable-functions-billing.md#azure-storage-transactions)).
* 최고의 도구 지원 - Azure Storage는 플랫폼 간 로컬 에뮬레이션을 제공하며 Visual Studio, Visual Studio Code 및 Azure Functions Core Tools와 통합됩니다.
* 높은 완성도 - Azure Storage는 최초로 개발되어 가장 많은 실전 테스트를 거친 Durable Functions용 스토리지 백엔드입니다.

Azure Storage 스토리지 공급자의 DTFx 구성 요소에 대한 소스 코드는 [Azure/durabletask](https://github.com/Azure/durabletask/tree/main/src/DurableTask.AzureStorage) GitHub 리포지토리에서 찾을 수 있습니다.

> [!NOTE]
> Azure Storage 공급자를 사용하는 경우 표준 범용 Azure Storage 계정이 필요합니다. 그 외의 스토리지 계정 유형은 지원되지 않습니다. 최신 v2 스토리지 계정은 Durable Functions 워크로드 비용이 훨씬 많이 발생할 수 있으므로 레거시 v1 범용 스토리지 계정을 사용하는 것이 좋습니다. Azure Storage 계정 유형에 대한 자세한 내용은 [Storage 계정 개요](../../storage/common/storage-account-overview.md) 설명서를 참조하세요.

## <a name="netherite-preview"></a><a name="netherite"></a>Netherite(미리 보기)

Netherite 스토리지 백 엔드는 [Microsoft Research](https://www.microsoft.com/research)에서 설계하고 개발했습니다. [Azure 페이지 Blob](../../storage/blobs/storage-blob-pageblob-overview.md)을 바탕으로 [Azure Event Hubs](../../event-hubs/event-hubs-about.md) 및 [더 빠른](https://www.microsoft.com/research/project/faster/) 데이터베이스 기술을 사용합니다. Netherite 디자인은 다른 공급자에 비해 오케스트레이션 및 엔터티 처리량이 훨씬 높습니다. 일부 벤치마크 시나리오에서는 기본 Azure Storage 공급자에 비해 처리량이 수십 배 증가하는 것으로 나타났습니다.

Netherite 스토리지 공급자의 주요 이점은 다음과 같습니다.

* 다른 스토리지 공급자에 비해 저렴한 비용으로 처리량이 크게 향상됩니다.
* 가격 대비 성능 최적화를 지원하므로 필요에 따라 성능을 스케일 업할 수 있습니다.
* Event Hubs Basic 및 Standard SKU에서 최대 32개의 데이터 파티션을 지원합니다.
* 처리량이 높은 워크로드에서 다른 공급자보다 비용 효율성이 높습니다.

시작하는 방법을 포함하여 Netherite 스토리지 공급자의 기술 세부 정보는 [Netherite 설명서](https://microsoft.github.io/durabletask-netherite)에서 확인할 수 있습니다. Netherite 스토리지 공급자의 소스 코드는 [microsoft/durabletask-netherite](https://github.com/microsoft/durabletask-netherite) GitHub 리포지토리에서 찾을 수 있습니다. Netherite 스토리지 공급자에 대한 자세한 평가는 다음 연구 논문 [Serverless Workflows with Durable Functions and Netherite](https://arxiv.org/abs/2103.00033)에서도 확인할 수 있습니다.

> [!NOTE]
> _Netherite_ 라는 이름의 기원은 [Minecraft](https://minecraft.fandom.com/wiki/Netherite)입니다.

## <a name="microsoft-sql-server-mssql-preview"></a><a name="mssql">MSSQL(Microsoft SQL Server)(미리 보기)</a>

MSSQL(Microsoft SQL Server) 스토리지 공급자는 모든 상태를 Microsoft SQL Server 데이터베이스에 유지합니다. [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)를 포함하여 SQL Server의 온-프레미스 및 클라우드 호스팅 배포와 호환됩니다.

MSSQL 스토리지 공급자의 주요 이점은 다음과 같습니다.

* 연결이 끊긴 환경 지원 - SQL Server 설치를 사용하는 경우 Azure 연결이 필요하지 않습니다.
* Azure 호스팅 및 온-프레미스를 포함하여 여러 환경과 클라우드 간에 이식 가능합니다.
* 데이터 일관성이 강력하기 때문에 데이터 손실 없이 백업/복원 및 장애 조치(failover)가 가능합니다.
* 사용자 지정 데이터 암호화를 기본 지원합니다(SQL Server의 기능).
* 기본 제공 저장 프로시저를 통해 기존 데이터베이스 애플리케이션과 통합됩니다.

시작하는 방법을 포함하여 MSSQL 스토리지 공급자의 기술 세부 정보는 [Microsoft SQL 공급자 설명서](https://microsoft.github.io/durabletask-mssql)에서 확인할 수 있습니다. MSSQL 스토리지 공급자의 소스 코드는 [microsoft/durabletask-mssql](https://github.com/microsoft/durabletask-mssql) GitHub 리포지토리에서 찾을 수 있습니다.

## <a name="configuring-alternate-storage-providers"></a>대체 스토리지 공급자 구성

대체 스토리지 공급자 구성은 일반적으로 다음과 같은 2단계 프로세스입니다.

1. 함수 앱에 적절한 NuGet 패키지를 추가합니다(확장 번들을 사용하는 앱의 경우 이 요구 사항은 일시적).
1. 사용하려는 스토리지 공급자를 지정하도록 **host.json** 파일을 업데이트합니다.

host.json 파일에서 스토리지 공급자를 명시적으로 구성하지 않으면 Azure Storage 공급자가 기본적으로 사용됩니다.

### <a name="configuring-the-azure-storage-provider"></a>Azure 스토리지 공급자 구성

Azure Storage 공급자는 기본 스토리지 공급자이며 명시적 구성, NuGet 패키지 참조 또는 확장 번들 참조가 필요하지 않습니다. **host.json** 구성 옵션의 전체 세트는 [여기](durable-functions-bindings.md#hostjson-settings)의 `extensions/durableTask/storageProvider` 경로 아래에서 찾을 수 있습니다.

### <a name="configuring-the-netherite-storage-provider"></a>Netherite 스토리지 공급자 구성

Netherite 스토리지 공급자를 사용하려면 먼저 **csproj** 파일(.NET 앱) 또는 **extensions.proj** 파일(JavaScript, Python 및 PowerShell 앱)에 [Microsoft.Azure.DurableTask.Netherite.AzureFunctions](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Netherite.AzureFunctions) NuGet 패키지에 대한 참조를 추가해야 합니다.

> [!NOTE]
> [확장 번들](../functions-bindings-register.md#extension-bundles)을 사용하는 앱에서는 아직 Netherite 스토리지 공급자가 지원되지 않습니다.

다음 host.json 예제에서는 Netherite 스토리지 공급자를 사용하는 데 필요한 최소 구성을 보여줍니다.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "Netherite",
        "storageConnectionName": "AzureWebJobsStorage",
        "eventHubsConnectionName": "EventHubsConnection"
      }
    }
  }
}
```

자세한 설치 지침은 [Netherite 시작 설명서](https://microsoft.github.io/durabletask-netherite/#/?id=getting-started)를 참조하세요.

### <a name="configuring-the-mssql-storage-provider"></a>MSSQL 스토리지 공급자 구성

MSSQL 스토리지 공급자를 사용하려면 먼저 **csproj** 파일(.NET 앱) 또는 **extensions.proj** 파일(JavaScript, Python 및 PowerShell 앱)에 [Microsoft.DurableTask.SqlServer.AzureFunctions](https://www.nuget.org/packages/Microsoft.DurableTask.SqlServer.AzureFunctions) NuGet 패키지에 대한 참조를 추가해야 합니다.

> [!NOTE]
> [확장 번들](../functions-bindings-register.md#extension-bundles)을 사용하는 앱에서는 아직 MSSQL 스토리지 공급자가 지원되지 않습니다.

다음 예제에서는 MSSQL 스토리지 공급자를 사용하는 데 필요한 최소 구성을 보여줍니다.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "mssql",
        "connectionStringName": "SQLDB_Connection"
      }
    }
  }
}
```

자세한 설치 지침은 [MSSQL 공급자 시작 설명서](https://microsoft.github.io/durabletask-mssql/#/quickstart)를 참조하세요.

## <a name="comparing-storage-providers"></a>스토리지 공급자 비교

지원되는 다양한 스토리지 공급자마다 득과 실이 있습니다. 다음 표를 참조하여 이러한 득실을 이해하고 요구 사항에 가장 적합한 스토리지 공급자를 결정할 수 있습니다.

| 스토리지 공급자 | Azure Storage | Netherite | MSSQL |
|- |-              |-          |-      |
| 공식 지원 상태 | ✅ GA(일반 공급) | ⚠ 공개 미리 보기 | ⚠ 공개 미리 보기 |
| 외부 종속성 | Azure Storage 계정(범용 v1) | Azure Event Hubs<br/>Azure Storage 계정(범용) | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) 또는 Azure SQL Database |
| 로컬 개발 및 에뮬레이션 옵션 | [Azurite v3.12 이상](../../storage/common/storage-use-azurite.md)(플랫폼 간)<br/>[Azure Storage Emulator](../../storage/common/storage-use-emulator.md)(Windows만) | 메모리 내 에뮬레이션([자세한 정보)](https://microsoft.github.io/durabletask-netherite/#/emulation) | SQL Server Developer 버전([Windows](/sql/database-engine/install-windows/install-sql-server), [Linux](/sql/linux/sql-server-linux-setup) 및 [Docker 컨테이너](/sql/linux/sql-server-linux-docker-container-deployment) 지원) |
| 작업 허브 구성 | 명시적 | 명시적 | 기본적으로 암시적([자세한 정보](https://microsoft.github.io/durabletask-mssql/#/taskhubs)) |
| 최대 처리량 | 보통 | 매우 높음 | 보통 |
| 최대 오케스트레이션/엔터티 스케일 아웃(노드 수) | 16 | 32 | 해당 없음 |
| 최대 작업 스케일 아웃(노드 수) | 해당 없음 | 32 | 해당 없음 |
| 사용량 플랜 지원 | ✅ 완벽하게 지원 | ❌ 지원 안 됨 | ❌ 지원 안 됨 |
| 탄력적 프리미엄 플랜 지원 | ✅ 완벽하게 지원 | ⚠ [런타임 스케일링 모니터링](../functions-networking-options.md#premium-plan-with-virtual-network-triggers) 필요 | ⚠ [런타임 스케일링 모니터링](../functions-networking-options.md#premium-plan-with-virtual-network-triggers) 필요 |
| [KEDA 2.0](https://keda.sh/) 스케일링 지원<br/>([자세한 정보](../functions-kubernetes-keda.md)) | ❌ 지원 안 됨 | ❌ 지원 안 됨 | ✅ [MSSQL 스케일러](https://keda.sh/docs/scalers/mssql/) 사용 지원([자세한 정보](https://microsoft.github.io/durabletask-mssql/#/scaling)) |
| [확장 번들](../functions-bindings-register.md#extension-bundles) 지원(비 .NET 앱에 권장) | ✅ 완벽하게 지원 | ❌ 지원 안 됨 | ❌ 지원 안 됨 |
| 가격-성능 구성 가능 여부 | ❌ 아니요 | ✅ 예(Event Hubs TU 및 CU) | ✅ 예(SQL vCPU) |
| 연결되지 않은 환경 지원 | ❌ Azure 연결 필요 | ❌ Azure 연결 필요 | ✅ 완벽하게 지원 |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Durable Functions 성능 및 스케일링에 대한 자세한 정보](durable-functions-perf-and-scale.md)
