---
title: 'CLI: Cosmos DB에 앱 연결'
description: Azure CLI를 사용하여 App Service 앱의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 앱을 MongoDB(Cosmos DB)에 연결하는 방법을 보여줍니다.
author: msangapu-msft
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 6beaafe19184e9c7b27c4e533f20c023948e9209
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736111"
---
# <a name="connect-an-app-service-app-to-cosmos-db-using-cli"></a>CLI를 사용하여 Cosmos DB에 App Service 앱 연결

이 샘플 스크립트는 Azure Cosmos DB의 MongoDB API 및 App Service 앱을 사용하여 Azure Cosmos DB 계정을 만듭니다. 그런 다음, 앱 설정을 사용하여 MongoDB 연결 문자열을 웹앱에 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 Azure CLI 버전 2.0 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, App Service 앱, Cosmos DB 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service 계획을 만듭니다. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service 앱을 만듭니다. |
| [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) | Cosmos DB 계정을 만듭니다. |
| [`az cosmosdb list-connection-strings`](/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | 지정된 Cosmos DB 계정에 대한 연결 문자열을 나열합니다. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | App Service 앱에 대한 앱 설정을 만들거나 업데이트합니다. 앱 설정은 앱에 대한 환경 변수로 노출됩니다([환경 변수 및 앱 설정 참조](../reference-app-settings.md) 참조). |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../samples-cli.md)에서 확인할 수 있습니다.
