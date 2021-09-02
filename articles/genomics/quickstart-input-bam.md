---
title: BAM 파일 입력을 사용하여 워크플로 제출
titleSuffix: Microsoft Genomics
description: 이 문서에서는 입력 파일이 단일 BAM 파일인 경우 Microsoft Genomics 서비스에 워크플로를 제출하는 방법을 보여 줍니다.
services: genomics
author: vigunase
manager: cgronlun
ms.author: vigunase
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 09e06e528f2e47338871d7a564b763534a363766
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255752"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>BAM 파일 입력을 사용하여 워크플로 제출

이 문서에서는 입력 파일이 단일 BAM 파일인 경우 Microsoft Genomics 서비스에 워크플로를 제출하는 방법을 보여 줍니다. 이 항목에서는 사용자가 이미 `msgen` 클라이언트를 설치하여 실행하고 있으며 Azure Storage 사용 방법에 익숙하다고 가정합니다. 제공된 샘플 데이터를 사용하여 워크플로를 성공적으로 제출한 경우 이 문서를 진행할 준비가 된 것입니다. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>설정: BAM 파일을 Azure Storage에 업로드합니다.
*reads.bam* 이라는 단일 BAM 파일이 있고 Azure의 개인 스토리지 계정인 *myaccount*(**https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/inputs/reads <span></span>.bam <span></span>**)에 업로드했다고 가정합니다. API URL과 액세스 키를 보유하고 있습니다. **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>** 에 출력하려 합니다.



## <a name="submit-your-job-to-the-msgen-client"></a>`msgen` 클라이언트에게 작업을 제출합니다. 


다음은 `msgen` 클라이언트에게 제공해야 하는 최소한의 인수 집합입니다. 쉽게 알 수 있도록 줄 바꿈이 추가되었습니다.

Windows의 경우:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Unix의 경우

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


구성 파일 사용을 선호하는 경우 다음 내용이 포함됩니다.

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

이 호출로 `config.txt` 파일 제출: `msgen submit -f config.txt`

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Storage에 BAM 파일을 업로드했고 `msgen` python 클라이언트를 통해 Microsoft Genomics 서비스에 워크플로를 제출했습니다. Microsoft Genomics 서비스에서 사용할 수 있는 워크플로 제출 및 기타 명령에 대한 추가 정보는 [FAQ](frequently-asked-questions-genomics.yml)를 참조하세요. 
