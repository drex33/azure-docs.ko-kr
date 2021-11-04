---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5813ec99666e7e758dd1d58a639211d3d0ca0c94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448952"
---
- 현재 미국 서 부 에서만 사용할 수 있습니다.
- 데이터 디스크에 대해서만 지원 됩니다.
- TiB 보다 작은 디스크는 가동 중지 시간 없이 4 TiB 이상 확장할 수 없습니다.
    - 디스크 크기를 4 TiB 이상으로 늘리면 가동 중지 시간 없이 확장할 수 있습니다.
- 다음 중 하나를 설치 하 고 사용 합니다.
    - [최신 Azure CLI](/cli/azure/install-azure-cli)
    - [최신 Azure PowerShell 모듈](/powershell/azure/install-az-ps)
    - 를 통해 액세스 하는 경우 Azure Portal입니다. [https://aka.ms/iaasexp/DiskLiveResize](https://aka.ms/iaasexp/DiskLiveResize)
    - 또는 2021-04-01 이상의 API 버전을 사용 하는 Azure Resource Manager 템플릿입니다.