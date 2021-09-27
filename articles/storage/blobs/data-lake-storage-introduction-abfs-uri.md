---
title: Azure Data Lake Storage Gen2 URI 사용
description: Azure Blob File System 드라이버(Azure Data Lake Storage Gen2용 Haadoop File System 드라이버)를 나타내는 abfs 체계 식별자에 대한 URI 구문을 알아봅니다.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: af31e49228f655d5b16cc20fdc07cd9a7da597d6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620220"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Azure Data Lake Storage Gen2 URI 사용

Azure Data Lake Storage Gen2와 호환되는 [Hadoop 파일 시스템](https://www.aosabook.org/en/hdfs.html) 드라이버는 해당 체계 식별자 `abfs`(Azure Blob 파일 시스템)로 인식됩니다. 다른 Hadoop 파일 시스템 드라이버와 일치하는 ABFS 드라이버는 Data Lake Storage Gen2 지원 계정 내에서 파일 및 디렉터리의 주소를 지정하기 위해 URI 형식을 사용합니다.

## <a name="uri-syntax"></a>URI 구문

Data Lake Storage Gen2에 대한 URI 구문은 스토리지 계정이 Data Lake Storage Gen2를 기본 파일 시스템으로 포함하도록 설정되었는지 여부에 따라 다릅니다.

주소를 지정할 Data Lake Storage Gen2 지원 계정이 계정을 만드는 동안 기본 파일 시스템으로 설정되지 **않은** 경우, 줄임 URI 구문은 다음과 같습니다.

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **체계 식별자**: `abfs` 프로토콜이 체계 식별자로 사용됩니다. 끝에 's'를 추가(abfs<b><i>s</i></b>)하면 ABFS Hadoop 클라이언트 드라이버인 <i>ALWAYS</i>는 선택한 인증 방법에 관계없이 항상 TLS(전송 계층 보안)를 사용합니다. OAuth를 인증으로 선택하는 경우 OAuth가 TLS 계층만 사용하므로 'abfss' 대신 'abfs'를 지정하는 경우에도 클라이언트 드라이버는 항상 TLS를 사용합니다. 마지막으로, 스토리지 계정 키의 이전 메서드를 사용하도록 선택하는 경우 클라이언트 드라이버는 TLS를 사용하지 않으려는 것으로 'abfs'를 해석합니다.

2. **파일 시스템**: 파일 및 폴더를 포함하는 부모 위치입니다. Azure Storage Blob 서비스의 컨테이너와 동일합니다.

3. **계정 이름**: 만드는 동안 스토리지 계정에 지정된 이름입니다.

4. **경로**: 디렉터리 구조의 슬래시 구분 기호(`/`) 표시입니다.

5. **파일 이름**: 개별 파일의 이름입니다. 이 매개 변수는 디렉터리의 주소를 지정하는 경우, 선택 사항입니다.

그렇지만 주소를 지정할 계정이 계정을 만드는 동안 기본 파일 시스템으로 설정되지 않은 경우, 줄임 URI 구문은 다음과 같습니다.

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **경로**: 디렉터리 구조의 슬래시 구분 기호(`/`) 표시입니다.

2. **파일 이름**: 개별 파일의 이름입니다.

## <a name="next-steps"></a>다음 단계

- [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
