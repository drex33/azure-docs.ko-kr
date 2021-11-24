---
title: 빠른 시작 - Azure CLI를 사용하여 Service Bus 큐 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Service Bus 네임스페이스를 만든 다음, 해당 네임스페이스에 큐를 만드는 방법에 대해 알아봅니다.
author: spelluru
ms.topic: quickstart
ms.date: 09/28/2021
ms.author: spelluru
ms.custom: mode-other
ms.openlocfilehash: f1897aae6701422592ede35c83abcf8589d29a77
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133057072"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Azure CLI를 사용하여 Service Bus 네임스페이스 및 큐 만들기
이 빠른 시작에서는 Azure CLI를 사용하여 Service Bus 네임스페이스 및 큐를 만드는 방법을 보여줍니다. 또한 클라이언트 애플리케이션이 큐에 메시지를 보내고 받는 데 사용할 수 있는 권한 부여 자격 증명을 가져오는 방법을 보여줍니다. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>사전 요구 사항
Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][free account]을 만들 수 있습니다.

이 빠른 시작에서는 Azure Portal에 로그인한 후에 시작할 수 있는 Azure Cloud Shell을 사용합니다. Azure Cloud Shell에 대한 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요. 머신에 Azure PowerShell을 [설치](/cli/azure/install-azure-cli)하고 사용할 수도 있습니다. 

## <a name="provision-resources"></a>리소스 프로비전
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 다음 이미지에 표시된 아이콘을 선택하여 Azure Cloud Shell을 시작합니다. Cloud Shell이 **PowerShell** 모드에 있으면 **Bash** 모드로 전환합니다. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell 시작":::
3. 다음 명령을 실행하여 Azure 리소스 그룹을 만듭니다. 원하는 경우 리소스 그룹 이름 및 위치를 업데이트합니다. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. 다음 명령을 입력하여 Service Bus 메시징 네임스페이스를 만듭니다.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. 다음 명령을 실행하여 이전 단계에서 만든 네임스페이스에 큐를 만듭니다. 이 예제에서 `ContosoRG`는 이전 단계에서 만든 리소스 그룹입니다. `ContosoSBusNS`는 Service 해당 리소스 그룹에서 만든 Bus 네임스페이스의 이름입니다. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. 다음 명령을 실행하여 네임스페이스에 대한 기본 연결 문자열을 가져옵니다. 이 연결 문자열을 사용하여 큐에 연결하고 메시지를 보내고 받습니다. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    연결 문자열과 큐 이름을 적어 둡니다. 메시지를 보내고 받는 데 사용합니다. 


## <a name="next-steps"></a>다음 단계
이 문서에서는 Service Bus 네임스페이스와 네임스페이스에 큐를 만들었습니다. 큐에 메시지를 보내거나 받는 방법에 대한 자세한 내용은 **메시지 보내기 및 받기** 섹션에서 다음 빠른 시작 중 하나를 참조하세요. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
