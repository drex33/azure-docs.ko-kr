---
title: GATK 리소스 번들
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 GATK 리소스 번들 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1b775d6ca0dd00a2e6eacf292c2f3397a0de303b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563023"
---
# <a name="gatk-resource-bundle"></a>GATK 리소스 번들

[GATK 리소스 번들](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle)은 GATK를 사용한 인간 유전자 재배열 데이터를 사용하기 위한 표준 파일의 컬렉션입니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>데이터 원본

이 데이터 세트는 https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle 에 있는 데이터 저장소의 미러입니다.

## <a name="data-volumes-and-update-frequency"></a>데이터 볼륨 및 업데이트 빈도

1. datasetgatkbestpractices: 542GB
1. datasetgatklegacybundles: 61GB
1. datasetgatktestdata: 2TB
1. datasetpublicbroadref: 477GB
1. datasetbroadpublic: 3TB

데이터 세트는 한 달에 한 번 매월 첫째 주에 업데이트됩니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 서부 2 및 미국 중서부 Azure 지역에 저장됩니다. 선호도를 위해 미국 서부 2 또는 미국 중서부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="data-access"></a>데이터 액세스

1. datasetgatkbestpractices

    미국 서부 2: 'https://datasetgatkbestpractices.blob.core.windows.net/dataset '
    
    미국 중서부: 'https://datasetgatkbestpractices-secondary.blob.core.windows.net/dataset '
    
    [SAS 토큰](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=6SaDfKtXAIfdpO%2BkvNA%2FsTNmNij%2Byh%2F%2F%2Bf98WAUqs7I%3D

2. datasetgatklegacybundles

    미국 서부 2: 'https://datasetgatklegacybundles.blob.core.windows.net/dataset '
    
    미국 중서부: 'https://datasetgatklegacybundles-secondary.blob.core.windows.net/dataset '
    
    [SAS 토큰](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=xBfxOPBqHKUCszzwbNCBYF0k9osTQjKnZbEjXCW7gU0%3D

3. datasetgatktestdata

    미국 서부 2: 'https://datasetgatktestdata.blob.core.windows.net/dataset '
    
    미국 중서부: 'https://datasetgatktestdata-secondary.blob.core.windows.net/dataset '
    
    [SAS 토큰](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=fzLts1Q2vKjuvR7g50vE4HteEHBxTcJbNvf%2FZCeDMO4%3D

4. datasetpublicbroadref
    
    미국 서부 2: 'https://datasetpublicbroadref.blob.core.windows.net/dataset '
    
    미국 중서부: 'https://datasetpublicbroadref-secondary.blob.core.windows.net/dataset '
    
    [SAS 토큰](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=DQxmjB4D1lAfOW9AxIWbXwZx6ksbwjlNkixw597JnvQ%3D

5. datasetbroadpublic

    미국 서부 2: 'https://datasetbroadpublic.blob.core.windows.net/dataset '
    
    미국 중서부: 'https://datasetbroadpublic-secondary.blob.core.windows.net/dataset '
    
    [SAS 토큰](../storage/common/storage-sas-overview.md): ?sv=2020-04-08&si=prod&sr=c&sig=u%2Bg2Ab7WKZEGiAkwlj6nKiEeZ5wdoJb10Az7uUwis%2Fg%3D

## <a name="use-terms"></a>사용 약관

[GATK 리소스 번들 공식 사이트](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle)를 방문하세요.

## <a name="contact"></a>연락처

[GATK 리소스 번들 공식 사이트](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle)를 방문하세요.

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.
