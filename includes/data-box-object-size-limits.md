---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 2978fff57cfc7ae3dd4629450f573758978058d5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130077840"
---
쓸 수 있는 Azure 개체의 크기는 다음과 같습니다. 업로드되는 모든 파일이 이러한 제한을 준수하는지 확인합니다.

| Azure 개체 형식 | 기본 제한                                             |
|-------------------|-----------------------------------------------------------|
| 블록 Blob        | ~ 4.75TiB                                                 |
| 페이지 Blob         | 8TiB <br> 페이지 Blob 형식으로 업로드되는 모든 파일은 정렬된 512바이트(정수의 배수)여야 하며, 그렇지 않은 경우 업로드되지 않습니다. <br> VHD 및 VHDX는 정렬된 512바이트입니다. |
| Azure 파일        | 1TiB                                                      |
| 관리 디스크     | 4TiB <br> 크기 및 제한에 대한 자세한 내용은 다음을 참조하세요. <li>[표준 SSD의 확장성 목표](../articles/virtual-machines/disks-types.md#standard-ssds)</li><li>[프리미엄 SSD의 확장성 목표](../articles/virtual-machines/disks-types.md#standard-hdds)</li><li>[표준 HDD의 확장성 목표](../articles/virtual-machines/disks-types.md#premium-ssds)</li><li>[관리 디스크의 가격 책정 및 요금 청구](../articles/virtual-machines/disks-types.md#billing)</li>  
