---
title: gnomAD(Genome Aggregation Database)
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 gnomAD(Genome Aggregation Database) 데이터 세트를 사용하는 방법에 대해 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 779f39afa7ac9b9890013db546223274cdf37ad6
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867668"
---
# <a name="genome-aggregation-database-gnomad"></a>gnomAD(Genome Aggregation Database)

[gnomAD(Genome Aggregation Database)](https://gnomad.broadinstitute.org/)는 다양한 대규모 시퀀싱 프로젝트에서 진유전체 및 게놈 시퀀싱 데이터를 집계하고 맞추며 더 광범위한 과학 커뮤니티에 요약 데이터를 제공하려는 목표로 국제 연구자 연합에서 개발한 리소스입니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>데이터 원본

이 데이터 세트는 Broad Institute와의 협업으로 호스트되며 전체 gnomAD 데이터 카탈로그는 https://gnomad.broadinstitute.org/downloads 에서 확인할 수 있습니다.

## <a name="data-volumes-and-update-frequency"></a>데이터 볼륨 및 업데이트 빈도

이 데이터 세트는 약 30TB의 데이터를 포함하며 각 gnomAD 릴리스로 업데이트됩니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트를 호스트하는 스토리지 계정은 미국 동부 Azure 지역에 있습니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="data-access"></a>데이터 액세스

스토리지 계정: ‘ https://datasetgnomad.blob.core.windows.net/dataset/ ’

이 데이터는 제한 없이 공개적으로 사용할 수 있으며, 대량 작업에는 AzCopy 도구를 사용하는 것이 좋습니다. 예를 들어 gnomAD 릴리스 3.0에서 VCF를 보려면:

```powershell
$ azcopy ls https://datasetgnomad.blob.core.windows.net/dataset/release/3.0/vcf/genomes
```

모든 VCF를 재귀적으로 다운로드하려면:

```powershell
$ azcopy cp --recursive=true https://datasetgnomad.blob.core.windows.net/dataset/release/3.0/vcf/genomes .
```

**신규: gnomAD v2.1.1 VCF 파일의 Parquet 형식(exomes 및 genomes)**

Parquet 파일을 보려면:

```powershell
$ azcopy ls https://datasetgnomadparquet.blob.core.windows.net/dataset
```

모든 Parquet 파일을 재귀적으로 다운로드하려면:

```powershell
$ cp --recursive=true https://datasetgnomadparquet.blob.core.windows.net/dataset
```

또한 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)는 gnomAD 릴리스의 파일 목록을 검색하는 데 유용한 도구입니다.

## <a name="use-terms"></a>사용 약관

데이터는 제한 없이 사용할 수 있습니다. 자세한 내용과 인용 세부 정보는 [gnomAD 정보 페이지](https://gnomad.broadinstitute.org/about)를 참조하세요.

## <a name="contact"></a>연락처

이 데이터 세트에 대한 질문이나 피드백은 [gnomAD 팀](https://gnomad.broadinstitute.org/contact)에 문의하세요.

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.
