---
title: Azure Queue Storage 소개 - Azure Storage
description: 대량의 메시지를 저장하기 위한 서비스인 Azure Queues Storage 소개를 참조하세요. Queue Storage 서비스에는 URL 형식, 스토리지 계정, 큐 및 메시지가 포함됩니다.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 1da3781b3dac4b79d8c8dc49decc669bfa7efde6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551739"
---
# <a name="what-is-azure-queue-storage"></a>Azure Queue Storage란?

Azure Queue Storage는 대량의 메시지를 저장하기 위한 서비스입니다. 전 세계 어디서나 인증된 호출을 통해 HTTP 또는 HTTPS를 사용하여 메시지에 액세스할 수 있습니다. 큐 메시지의 크기는 최대 64KB입니다. 큐는 스토리지 계정의 용량 제한에 도달할 때까지 수백만 개의 메시지를 포함할 수 있습니다. 큐는 비동기적으로 처리할 작업의 백로그를 만드는 데 일반적으로 사용됩니다.

## <a name="queue-storage-concepts"></a>Queue Storage 개념

Queue Storage에는 다음 구성 요소가 포함됩니다.

![스토리지 계정, 큐 및 메시지 간의 관계를 보여주는 다이어그램.](./media/storage-queues-introduction/queue1.png)

- **URL 형식:** 다음 URL 형식을 사용하여 큐에 주소를 지정할 수 있습니다.

  `https://<storage account>.queue.core.windows.net/<queue>`

  다음 URL은 다이어그램에 있는 큐의 주소를 지정합니다.

  `https://myaccount.queue.core.windows.net/images-to-download`

- **스토리지 계정:** Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. 스토리지 계정 용량에 대한 자세한 내용은 [표준 스토리지 계정의 확장성 및 성능 목표](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 참조하세요.

- **큐:** 큐에는 메시지 집합이 포함됩니다. 큐 이름은 **반드시** 모두 소문자여야 합니다. 큐 이름 지정에 대한 자세한 내용은 [큐 및 메타데이터 이름 지정](/rest/api/storageservices/naming-queues-and-metadata)을 참조하세요.

- **메시지:** 최대 64KB인 임의 형식의 메시지입니다. 2017-07-29 미만 버전에서 허용되는 최대 TTL(Time to Live)은 7일입니다. 2017-07-29 이상 버전에서 허용되는 최대 TTL(Time to Live)은 모든 양수 또는 메시지가 만료되지 않는 -1입니다. 이 매개 변수를 생략하면 기본 TTL(Time to Live)은 7일입니다.

## <a name="next-steps"></a>다음 단계

- [스토리지 계정을 만드는](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [.NET을 사용하여 Queue Storage 시작](storage-dotnet-how-to-use-queues.md)
- [Java를 사용하여 Queue Storage 시작](storage-java-how-to-use-queue-storage.md)
- [Python을 사용하여 Queue Storage 시작](storage-python-how-to-use-queue-storage.md)
- [Node.js를 사용하여 Queue Storage 시작](storage-nodejs-how-to-use-queues.md)
