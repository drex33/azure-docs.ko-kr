---
title: 빠른 시작 - Azure CLI를 사용하여 Web PubSub 인스턴스 만들기
description: Azure CLI를 사용하여 Web PubSub 인스턴스를 만드는 방법을 보여주는 빠른 시작
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.custom: mode-api
ms.openlocfilehash: d39b005b87728f21fa21d05eab15eadd4848f97b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133068672"
---
# <a name="quickstart-create-a-web-pubsub-instance-with-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Web PubSub 인스턴스 만들기

[Azure CLI(Azure 명령줄 인터페이스)](/cli/azure)는 Azure 리소스를 만들고 관리하는 데 사용되는 명령 세트입니다. Azure CLI는 모든 Azure 서비스에서 사용할 수 있으며, Azure를 빠르게 사용할 수 있도록 자동화에 초점을 두고 설계되었습니다. 이 빠른 시작에서는 Azure CLI를 사용하여 Azure Web PubSub 인스턴스를 만드는 옵션을 보여줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.22.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Web PubSub 인스턴스 만들기

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="try-the-newly-created-instance"></a>새로 만든 인스턴스 시도

> [!div class="nextstepaction"]
> [CLI를 사용하여 새로 만든 인스턴스 시도](./quickstart-cli-try.md#play-with-the-instance)

> [!div class="nextstepaction"]
> [브라우저에서 새로 만든 인스턴스 시도](./quickstart-live-demo.md#try-the-instance-with-an-online-demo)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Clean up resource](includes/cli-delete-resources.md)]

## <a name="next-steps"></a>다음 단계

실제 애플리케이션에서는 다양한 언어의 SDK를 사용하여 고유한 애플리케이션을 빌드할 수 있습니다. 또한 서버리스 애플리케이션을 쉽게 빌드할 수 있도록 함수 확장도 제공합니다.

[!INCLUDE [next step](includes/include-next-step.md)]
