---
title: 빠른 시작 - Azure Web PubSub 인스턴스에 연결 및 재생
description: Azure CLI에서 인스턴스를 재생하는 방법을 보여 주는 빠른 시작
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: 99045ab4675527f4f0a821025bf6f870d63bb296
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131998071"
---
# <a name="quickstart-connect-to-the-azure-web-pubsub-instance-from-cli"></a>빠른 시작: CLI에서 Azure Web PubSub 인스턴스에 연결

이 빠른 시작에서는 [Azure CLI(Azure 명령줄 인터페이스)](/cli/azure)를 사용하여 Azure Web PubSub 인스턴스에 연결하고 연결된 클라이언트에 메시지를 게시하는 방법을 보여 줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.22.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Web PubSub 인스턴스 만들기

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="play-with-the-instance"></a>인스턴스 재생

[!INCLUDE [Try the Web PubSub instance](includes/cli-awps-client.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Web PubSub 서비스에 연결하는 방법과 연결된 클라이언트에 메시지를 게시하는 방법에 대한 기본 개념을 알아보았습니다.

실제 애플리케이션에서는 다양한 언어의 SDK를 사용하여 고유한 애플리케이션을 빌드할 수 있습니다. 또한 서버리스 애플리케이션을 쉽게 빌드할 수 있도록 함수 확장도 제공합니다.

[!INCLUDE [next step](includes/include-next-step.md)]
