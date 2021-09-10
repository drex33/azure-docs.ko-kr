---
title: 공유 VM 이미지를 사용하여 Azure CLI에서 확장 집합 만들기
description: Azure CLI를 사용하여 Azure에서 가상 머신 확장 집합 배포에 사용할 공유 VM 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: cynthn
ms.reviewer: mimckitt
ms.openlocfilehash: b3131d811b35cdbe28018a6939e514df7458cc95
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693196"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 가상 머신 확장 집합용 공유 이미지 만들기 및 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

확장 집합을 만들 때 VM 인스턴스 배포 시 사용할 이미지를 지정합니다. [공유 이미지 갤러리](../virtual-machines/shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 

Shared Image Gallery를 사용하면 이미지를 다른 사용자와 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>다음 단계

[VM](../virtual-machines/image-version-vm-cli.md) 또는 [관리형 이미지](../virtual-machines/image-version-managed-image-cli.md)에서 이미지 버전을 만듭니다.

공유 이미지 갤러리에 대한 자세한 내용은 [개요](../virtual-machines/shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](../virtual-machines/troubleshooting-shared-images.md)을 참조하세요.
