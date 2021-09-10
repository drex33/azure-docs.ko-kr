---
title: PowerShell을 사용하여 이미지 리소스 나열, 업데이트 및 삭제
description: Azure PowerShell을 사용하여 공유 이미지 갤러리에서 이미지 리소스를 나열, 업데이트 및 삭제합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/27/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 91664719f8af1cd44336a7701cd561eeea6d8279
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698417"
---
# <a name="list-update-and-delete-image-resources-using-powershell"></a>PowerShell을 사용하여 이미지 리소스 나열, 업데이트 및 삭제 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Azure PowerShell을 사용하여 공유 이미지 갤러리 리소스를 관리할 수 있습니다.

[!INCLUDE [virtual-machines-common-gallery-list-ps.md](../../includes/virtual-machines-common-gallery-list-ps.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-ps](../../includes/virtual-machines-common-shared-images-update-delete-ps.md)]

## <a name="next-steps"></a>다음 단계

[Azure Image Builder(미리 보기)](./image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](./linux/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다.