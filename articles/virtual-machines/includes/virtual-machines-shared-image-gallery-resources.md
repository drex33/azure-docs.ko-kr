---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7e5ab96e543a61cb45f3ebc6792b39a115916cd0
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449455"
---
| 리소스 | 설명|
|----------|------------|
| **이미지 원본** | 이는 이미지 갤러리에서 **이미지 버전** 을 만드는 데 사용할 수 있는 리소스입니다. 이미지 소스는 [일반화 또는 특수화](../shared-image-galleries.md#generalized-and-specialized-images)된 기존 Azure VM, 관리형 이미지, 스냅샷 또는 다른 이미지 갤러리의 이미지 버전일 수 있습니다. |
| **이미지 갤러리** | Azure Marketplace와 마찬가지로 **이미지 갤러리** 는 이미지를 관리하고 공유하는 데 사용되는 리포지토리이지만 액세스할 수 있는 사람을 제어할 수 있습니다. |
| **이미지 정의** | 이미지 정의는 갤러리 내에 생성되고, 내부적으로 사용하기 위해 충족해야 할 요구 사항과 이미지에 대한 정보를 전달합니다. 여기에는 이미지가 Windows인지, Linux인지 여부, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함됩니다. 이미지의 형식 정의입니다. |
| **이미지 버전** | **이미지 버전** 은 갤러리를 사용하는 경우 VM을 만들 때 사용합니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. 관리되는 이미지와 마찬가지로 **이미지 버전** 을 사용하여 VM을 만들 때는 이미지 버전을 사용하여 VM의 새 디스크를 만듭니다. 이미지 버전은 여러 번 사용할 수 있습니다. |