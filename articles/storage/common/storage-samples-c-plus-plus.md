---
title: C++를 사용한 Azure Storage 샘플 | Microsoft Docs
description: Azure Storage에 대한 샘플 코드 및 애플리케이션을 확인하고 다운로드하여 실행합니다. C++ 스토리지 클라이언트 라이브러리를 사용하여 Blob, 큐, 테이블 및 파일에 대한 예제 시작을 검색합니다.
author: normesta
ms.author: normesta
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 186abd4f318820b1ffb63b6769bb37f4adf69175
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133266354"
---
# <a name="azure-storage-samples-using-v12-c-client-libraries"></a>v12 C++ 클라이언트 라이브러리를 사용하는 Azure Storage 샘플

다음 테이블에서는 샘플 리포지토리 및 각 샘플에서 다루는 시나리오에 대한 개요를 제공합니다. GitHub에서 해당 샘플 코드를 보려면 링크를 클릭합니다.

> [!NOTE]
> 이러한 샘플에서는 최신 Azure Storage C++ v12 라이브러리를 사용합니다.

## <a name="blob-samples"></a>Blob 샘플

:::row:::
   :::column:::
        [연결 문자열을 사용하여 인증](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/samples/blob_getting_started.cpp#L18)
   :::column-end:::
   :::column:::
        [Blob 컨테이너 만들기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/samples/blob_getting_started.cpp#L20)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Blob 클라이언트 가져오기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/samples/blob_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [Blob 업로드](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/samples/blob_getting_started.cpp#L32)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [Blob에 메타데이터 설정](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/samples/blob_getting_started.cpp#L34)
   :::column-end:::
   :::column:::
        [Blob 속성 가져오기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/samples/blob_getting_started.cpp#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
        [Blob 다운로드](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-blobs/samples/blob_getting_started.cpp#L44)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2 샘플

:::row:::
   :::column:::
        [연결 문자열을 사용하여 서비스 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L22)
   :::column-end:::
   :::column:::
        [연결 문자열을 사용하여 파일 시스템 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L25)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [파일 시스템 만들기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L30)
   :::column-end:::
   :::column:::
        [디렉터리 만들기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L48)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [파일 만들기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L52)
   :::column-end:::
   :::column:::
        [파일에 데이터 추가](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [파일 데이터 플러시](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L77)
   :::column-end:::
   :::column:::
        [파일 읽기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
        [모든 파일 시스템 나열](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L88)
   :::column-end:::
   :::column:::
        [파일 시스템 삭제](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-datalake/samples/datalake_getting_started.cpp#L102)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure Files 샘플

:::row:::
    :::column:::
        [연결 문자열을 사용하여 공유 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/samples/file_share_getting_started.cpp#L18)
    :::column-end:::
    :::column:::
        [파일 공유 만들기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/samples/file_share_getting_started.cpp#L21)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [파일 클라이언트 가져오기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/samples/file_share_getting_started.cpp#L29)
    :::column-end:::
    :::column:::
        [파일 업로드](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/samples/file_share_getting_started.cpp#L31)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [파일에 메타데이터 설정](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/samples/file_share_getting_started.cpp#L33)
    :::column-end:::
    :::column:::
        [파일 속성 가져오기](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/samples/file_share_getting_started.cpp#L36)
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="2":::
        [파일 다운로드](https://github.com/Azure/azure-sdk-for-cpp/blob/master/sdk/storage/azure-storage-files-shares/samples/file_share_getting_started.cpp#L43)
    :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Azure 코드 샘플 라이브러리

전체 C++ 샘플 라이브러리를 보려면 다음으로 이동하세요.

- [Azure Blob 코드 샘플](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/samples)
- [Azure Data Lake 코드 샘플](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-datalake/samples)
- [Azure Files 코드 샘플](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-files-shares/samples)

각 라이브러리에 대한 GitHub 리포지토리를 찾아보고 복제할 수 있습니다.

## <a name="getting-started-guides"></a>시작 가이드

Azure Storage 클라이언트 라이브러리를 설치하고 시작하는 방법에 대한 지침은 찾고 있는 경우 다음 가이드를 확인해 보세요.

- [빠른 시작: Azure Blob 스토리지 라이브러리 v12 - C++](../blobs/quickstart-blobs-c-plus-plus.md)

## <a name="next-steps"></a>다음 단계

다른 언어용 샘플에 대한 정보:

- .NET: [.NET을 사용한 Azure Storage 샘플](storage-samples-dotnet.md)
- Java: [Java를 사용한 Azure Storage 샘플](storage-samples-java.md)
- Python: [Python을 사용한 Azure Storage 샘플](storage-samples-python.md)
- JavaScript/node.js: [JavaScript를 사용한 Azure Storage 샘플](storage-samples-javascript.md)
- 모든 기타 언어: [Azure Storage 샘플](storage-samples.md)
