---
title: Azure CLI를 사용하여 공유 이미지 갤러리 만들기
description: 이 문서에서는 Azure CLI를 사용하여 Azure에서 VM의 공유 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: cf675da2dd921d588d8130edaccc2b7db6e8f881
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695808"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Azure CLI를 사용하여 Shared Image Gallery 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

[공유 이미지 갤러리](./shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 

Shared Image Gallery를 사용하면 사용자 지정 VM 이미지를 다른 사용자와 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>다음 단계

Azure CLI를 사용하여 [VM](image-version-vm-cli.md) 또는 [관리형 이미지](image-version-managed-image-cli.md)에서 이미지 버전을 만듭니다.

공유 이미지 갤러리에 대한 자세한 내용은 [개요](./shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 참조하세요.