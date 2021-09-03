---
title: 포함 파일
description: 포함 파일
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c1fda4eb853e97b7a5b85beebe162ab3869e4a68
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "113799164"
---
Azure 파일 공유는 Azure 스토리지 계정의 클라우드에 저장됩니다.
다른 수준의 성능 고려 사항이 여기에 적용됩니다.

활성 공유(많은 사용자 및/또는 애플리케이션에서 사용하는 공유)가 많은 경우 두 개의 Azure 파일 공유가 스토리지 계정의 성능 제한에 도달할 수 있습니다.

가장 좋은 방법은 각각 파일 공유가 하나씩 있는 스토리지 계정을 배포하는 것입니다.
보관 공유가 있거나 일상 활동이 적을 것으로 예상되는 경우 여러 Azure 파일 공유를 동일한 스토리지 계정으로 풀할 수 있습니다.

이러한 고려 사항은 Azure 파일 동기화보다 직접 클라우드 액세스(Azure VM을 통해)에 더 많이 적용됩니다. 이러한 공유에서 Azure 파일 동기화만 사용하려는 경우 여러 개를 단일 Azure Storage 계정으로 그룹화하는 것이 좋습니다.

공유 목록을 만든 경우 각 공유를 해당 스토리지 계정에 매핑해야 합니다.

이전 단계에서는 적절한 공유 수를 결정했습니다. 이 단계에서는 스토리지 계정을 파일 공유에 매핑합니다. 이제 적절한 수의 Azure 파일 공유가 포함된 적절한 수의 Azure 스토리지 계정을 배포합니다.

각 스토리지 계정의 지역이 동일하고 이미 배포한 Storage Sync Service 리소스의 지역과 일치해야 합니다.

> [!CAUTION]
> 100TiB 한도가 있는 Azure 파일 공유를 만드는 경우 해당 공유는 로컬 중복 스토리지 또는 영역 중복 스토리지 중복 옵션만 사용할 수 있습니다. 100TiB 파일 공유를 사용하려면 먼저 스토리지 중복도 요구 사항을 고려하세요.

Azure 파일 공유는 기본적으로 여전히 5TiB 한도로 생성됩니다. [Azure 파일 공유 만들기](../articles/storage/files/storage-how-to-create-file-share.md)의 단계에 따라 대량 파일 공유를 만듭니다.

스토리지 계정을 배포할 때 또 다른 고려 사항은 Azure Storage의 중복도입니다. [Azure Storage 중복도 옵션](../articles/storage/common/storage-redundancy.md)을 참조하세요.

리소스의 이름도 중요합니다. 예를 들어 HR 부서의 여러 공유를 Azure 스토리지 계정 하나로 그룹화하는 경우 스토리지 계정의 이름을 적절하게 지정해야 합니다. 마찬가지로 Azure 파일 공유의 이름을 지정할 때 온-프레미스 파일 공유에 사용되는 이름과 비슷한 이름을 사용해야 합니다.