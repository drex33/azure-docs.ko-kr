---
title: Azure HPC Cache 데이터 수집 - msrsync
description: Azure HPC Cache에서 msrsync를 사용하여 Blob 스토리지 대상으로 데이터를 이동하는 방법입니다.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: femila
ms.openlocfilehash: 6ce1cb90f36b87cf10521b7ffbafa9a16bd62aef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088182"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC Cache 데이터 수집 - msrsync 메서드

이 문서에서는 Azure HPC Cache와 함께 사용할 수 있도록 ``msrsync`` 유틸리티를 사용하여 Azure Blob 스토리지 컨테이너에 데이터를 복사하는 방법에 대한 자세한 지침을 제공합니다.

Azure HPC Cache에 사용하도록 Blob 스토리지로 데이터를 이동하는 방법에 대한 자세한 내용은 [Move data to Azure Blob storage](hpc-cache-ingest.md)(Azure Blob 스토리지로 데이터 이동)를 참조하세요.

``msrsync`` 도구를 사용하여 Azure HPC Cache의 백 엔드 스토리지 대상으로 데이터를 이동할 수 있습니다. 이 도구는 여러 개의 ``rsync`` 병렬 프로세스를 실행하여 대역폭 사용량을 최적화하도록 설계되었습니다. GitHub의 https://github.com/jbd/msrsync에서 사용할 수 있습니다.

``msrsync``는 원본 디렉터리를 별도의 "버킷"으로 분할한 다음, 각 버킷에서 ``rsync`` 프로세스를 개별적으로 실행합니다.

4개 코어 VM을 사용한 예비 테스트에서는 64개의 프로세스를 사용할 때 최고의 효율성을 보였습니다. ``msrsync`` 옵션인 ``-p``를 사용하여 프로세스 수를 64로 설정합니다.

``msrsync``는 로컬 볼륨 간에만 쓸 수 있습니다. 원본 및 대상은 명령을 실행하는 데 사용되는 워크스테이션에서 로컬 탑재로 액세스할 수 있어야 합니다.

다음 지침에 따라 ``msrsync``를 사용하여 Azure Blob 스토리지를 Azure HPC Cache로 채웁니다.

1. ``msrsync`` 및 해당 필수 구성 요소(``rsync`` 및 Python 2.6 이상)를 설치합니다.
1. 복사할 파일 및 디렉터리의 총 수를 결정합니다.

   예를 들어 인수가 포함된 ``prime.py`` 유틸리티 ```prime.py --directory /path/to/some/directory```를 사용합니다(<https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>를 다운로드하여 사용할 수 있음).

   ``prime.py``를 사용하지 않는 경우 다음과 같이 ``find`` GNU 도구를 사용하여 항목의 수를 계산할 수 있습니다.

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 항목 수를 64로 나누어 프로세스당 항목 수를 결정합니다. 명령을 실행할 때 ``-f`` 옵션에 이 숫자를 사용하여 버킷의 크기를 설정합니다.

1. ``msrsync`` 명령을 실행하여 파일을 복사합니다.

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   예를 들어 이 명령은 64개 프로세스의 11,000개 파일을 /test/source-repository에서 /mnt/hpccache/repository로 이동하도록 설계되었습니다.

   `mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository`
