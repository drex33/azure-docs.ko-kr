---
title: Azure Functions 타이머를 사용하여 일정에 따라 Azure Cosmos DB 조정
description: PowerShell 및 Azure Functions를 사용하여 Azure Cosmos DB 처리량을 조정하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: f36fc116f2e32d6b0946e17383163e764d1186a0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117105"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Azure Functions 타이머 트리거를 사용하여 Azure Cosmos DB 처리량 조정
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos 계정의 성능은 초당 요청 단위(RU/s)로 표현된 프로비전된 처리량을 기반으로 합니다. 프로비저닝은 두 번째 단위이며 시간당 최고 RU/s를 기준으로 요금이 청구됩니다. 이러한 프로비전된 용량 모델을 사용하면 서비스는 짧은 대기 시간 및 높은 가용성이 보장되는 예측 가능하고 일관된 처리량을 제공할 수 있습니다. 대부분의 프로덕션 워크로드는 이러한 기능을 제공합니다. 하지만 Azure Cosmos DB가 작업 시간 동안만 사용되는 개발 및 테스트 환경에서 오전에 처리량을 확장하고 작업 시간 이후 저녁에 축소할 수 있습니다.

코어(SQL) API 계정에 대해 [Azure Resource Manager 템플릿](./templates-samples-sql.md), [Azure CLI](cli-samples.md) 및 [PowerShell](powershell-samples.md)을 통해 또는 언어별 Azure Cosmos DB SDK를 사용하여 처리량을 설정할 수 있습니다. 리소스 관리자 템플릿, Azure CLI 또는 PowerShell을 사용할 때 얻는 혜택은 모든 Azure Cosmos DB 모델 API를 지원한다는 것입니다.

## <a name="throughput-scheduler-sample-project"></a>처리량 스케줄러 샘플 프로젝트

일정에 따라 Azure Cosmos DB의 크기를 조정하는 프로세스를 간소화하기 위해 [Azure Cosmos 처리량 스케줄러](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)라는 샘플 프로젝트를 만들었습니다. 이 프로젝트는 두 개의 타이머 트리거("ScaleUpTrigger" 및 "ScaleDownTrigger")가 있는 Azure Functions 앱입니다. 트리거는 각 트리거의 `resources.json` 파일에 정의된 대로 각 리소스에서 처리량을 설정하는 PowerShell 스크립트를 실행합니다. ScaleUpTrigger는 오전 8시(UTC)에 실행하도록 구성되고 ScaleDownTrigger는 오후 6시(UTC)에 실행하도록 구성되며 이러한 시간은 각 트리거의 `function.json` 파일 내에서 쉽게 업데이트할 수 있습니다.

이 프로젝트를 로컬로 복제하고, 수정하여 확장 및 축소할 Azure Cosmos DB 리소스 및 실행할 일정을 지정할 수 있습니다. 나중에 Azure Cosmos 계정에 대한 처리량을 설정하는 "Azure Cosmos DB 연산자" 역할을 가진 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control.md) 권한으로 Azure 구독에 배포하고 관리 서비스 ID를 사용하여 보안을 유지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Azure Cosmos DB 처리량 스케줄러](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)에서 샘플을 다운로드하세요.