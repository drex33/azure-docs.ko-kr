---
title: Java를 사용한 Azure Storage 샘플 | Microsoft Docs
description: Azure Storage에 대한 샘플 코드 및 애플리케이션을 확인하고 다운로드하여 실행합니다. Java 스토리지 클라이언트 라이브러리를 사용하여 BLOB, 큐, 테이블 및 파일에 대한 예제 시작을 검색합니다.
author: normesta
ms.custom: devx-track-java
ms.author: normesta
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 0d5a6c98e990bdc87fbe3dcb26cfe4710b3af099
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588659"
---
# <a name="azure-storage-samples-using-v12-java-client-libraries"></a>v12 Java 클라이언트 라이브러리를 사용하는 Azure Storage 샘플

다음 테이블에서는 샘플 리포지토리 및 각 샘플에서 다루는 시나리오에 대한 개요를 제공합니다. GitHub에서 해당 샘플 코드를 보려면 링크를 클릭합니다.

> [!NOTE]
> 이러한 샘플에서는 최신 Azure Storage Java v12 라이브러리를 사용합니다. 레거시 v8 코드는 GitHub 리포지토리의 [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started)을 참조하세요.

## <a name="blob-samples"></a>Blob 샘플

### <a name="authentication"></a>인증

:::row:::
   :::column span="":::
      [공유 키 자격 증명을 사용하여 인증](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L38)
   :::column-end:::
   :::column span="":::
      [Azure ID를 사용하여 인증](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/AzureIdentityExample.java#L10)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Blob service

:::row:::
   :::column span="":::
      [Blob 서비스 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L49)
   :::column-end:::
   :::column span="":::
      [컨테이너 나열](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/ListContainersExample.java#L10)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [컨테이너 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/ListContainersExample.java#L52)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>일괄 처리

:::row:::
   :::column span="":::
      [Blob 일괄 처리 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L41)
   :::column-end:::
   :::column span="":::
      [Blob 대량 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Blob 일괄 처리에 대한 액세스 계층 설정](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L51)
   :::column-end:::
:::row-end:::

### <a name="container"></a>컨테이너

:::row:::
   :::column span="":::
      [컨테이너 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L57)
   :::column-end:::
   :::column span="":::
      [컨테이너 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L64)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Blob 나열](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L112)
   :::column-end:::
   :::column span="":::
      [컨테이너 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L123)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [Blob 업로드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L79)
   :::column-end:::
   :::column span="":::
      [Blob 다운로드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Blob 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L118)
   :::column-end:::
   :::column span="":::
      [큰 파일에서 Blob 업로드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/FileTransferExample.java#L95)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [파일에 큰 Blob 다운로드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/FileTransferExample.java#L100)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>문제 해결

:::row:::
   :::column span="2":::
      [컨테이너 클라이언트를 사용하여 복구 가능한 오류 트리거](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/StorageErrorHandlingExample.java#L11)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2 샘플

### <a name="data-lake-service"></a>Data Lake 서비스

:::row:::
   :::column span="":::
      [Data Lake 서비스 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L48)
   :::column-end:::
   :::column span="":::
      [파일 시스템 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L57)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>파일 시스템

:::row:::
   :::column span="":::
      [파일 시스템 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L64)
   :::column-end:::
   :::column span="":::
      [디렉터리 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [파일 및 하위 디렉터리 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L73)
   :::column-end:::
   :::column span="":::
      [파일 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L83)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [파일 시스템의 경로 나열](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L131)
   :::column-end:::
   :::column span="":::
      [파일 시스템 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L142)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Azure 스토리지 계정의 파일 시스템 나열](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/ListFileSystemsExample.java#L10)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>디렉터리

:::row:::
   :::column span="":::
      [디렉터리 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L31)
   :::column-end:::
   :::column span="":::
      [부모 디렉터리 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [자식 디렉터리 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L44)
   :::column-end:::
   :::column span="":::
      [자식 디렉터리에 파일 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [디렉터리 속성 가져오기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L68)
   :::column-end:::
   :::column span="":::
      [자식 디렉터리 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L83)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [부모 폴더 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L90)
   :::column-end:::
:::row-end:::

### <a name="file"></a>파일

:::row:::
   :::column span="":::
      [파일 클라이언트를 사용하여 파일 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L93)
   :::column-end:::
   :::column span="":::
      [파일 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L137)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [파일에 대한 액세스 제어 설정](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/GetSetAccessControlExample.java#L82)
   :::column-end:::
   :::column span="":::
      [파일에 대한 액세스 제어 가져오기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/GetSetAccessControlExample.java#L104)
   :::column-end:::
:::row-end:::

## <a name="azure-file-samples"></a>Azure File 샘플

### <a name="authentication"></a>인증

:::row:::
   :::column span="2":::
      [연결 문자열을 사용하여 인증](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L27)
   :::column-end:::
:::row-end:::

### <a name="file-service"></a>파일 서비스

:::row:::
   :::column span="":::
      [파일 공유 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L31)
   :::column-end:::
   :::column span="":::
      [속성 가져오기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L40)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [공유 나열](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L49)
   :::column-end:::
   :::column span="":::
      [공유 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L49)
   :::column-end:::
:::row-end:::

### <a name="file-share"></a>파일 공유

:::row:::
   :::column span="":::
      [공유 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L29)
   :::column-end:::
   :::column span="":::
      [공유 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L40)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [공유 스냅샷 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L55)
   :::column-end:::
   :::column span="":::
      [공유 클라이언트를 사용하여 디렉터리 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L63)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [공유 속성 가져오기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L72)
   :::column-end:::
   :::column span="":::
      [루트 디렉터리 및 목록 디렉터리 가져오기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L100)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [공유 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L151)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>디렉터리

:::row:::
   :::column span="":::
      [부모 디렉터리 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L35)
   :::column-end:::
   :::column span="":::
      [자식 디렉터리 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L42)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [자식 디렉터리에 파일 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L50)
   :::column-end:::
   :::column span="":::
      [디렉터리 및 파일 나열](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [자식 폴더 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L90)
   :::column-end:::
   :::column span="":::
      [부모 폴더 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L97)
   :::column-end:::
:::row-end:::

### <a name="file"></a>파일

:::row:::
   :::column span="":::
      [파일 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L45)
   :::column-end:::
   :::column span="":::
      [파일 업로드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [파일 다운로드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L100)
   :::column-end:::
   :::column span="":::
      [파일 속성 가져오기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L120)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [파일 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L128)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>큐 샘플

### <a name="authentication"></a>인증

:::row:::
   :::column span="2":::
      [SAS 토큰을 사용하여 인증](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L17)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>큐 서비스

:::row:::
   :::column span="":::
      [큐 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L20)
   :::column-end:::
   :::column span="":::
      [큐 나열](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L22)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [큐 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L27)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>큐

:::row:::
   :::column span="":::
      [큐 클라이언트 만들기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L25)
   :::column-end:::
   :::column span="":::
      [큐에 메시지 추가](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L27)
   :::column-end:::
:::row-end:::

### <a name="message"></a>메시지

:::row:::
   :::column span="":::
      [메시지 수 가져오기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L34)
   :::column-end:::
   :::column span="":::
      [메시지 보기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [메시지 받기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L41)
   :::column-end:::
   :::column span="":::
      [메시지 업데이트](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [첫 번째 메시지 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L50)
   :::column-end:::
   :::column span="":::
      [모든 메시지 지우기](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L59)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [큐 삭제](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L64)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>테이블 샘플(v11)

:::row:::
   :::column span="":::
      [테이블 만들기](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L50)
   :::column-end:::
   :::column span="":::
      [엔터티/테이블 삭제](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L109)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [엔터티 삽입/병합/바꾸기](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L195)
   :::column-end:::
   :::column span="":::
      [엔터티 쿼리](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L234)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [테이블 쿼리](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L262)
   :::column-end:::
   :::column span="":::
      [테이블 ACL/속성](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [엔터티 업데이트](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L76)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Azure 코드 샘플 라이브러리

전체 Java 샘플 라이브러리를 보려면 다음으로 이동하세요.

- [Azure Blob 코드 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)
- [Azure Data Lake 코드 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake)
- [Azure Files 코드 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share)
- [Azure 큐 코드 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)

각 라이브러리에 대한 GitHub 리포지토리를 찾아보고 복제할 수 있습니다.

## <a name="getting-started-guides"></a>시작 가이드

Azure Storage 클라이언트 라이브러리를 설치하고 시작하는 방법에 대한 지침은 찾고 있는 경우 다음 가이드를 확인해 보세요.

- [Java에서 Azure Blob Service 시작](../blobs/storage-quickstart-blobs-java.md)
- [Java에서 Azure Queue Service 시작](../queues/storage-quickstart-queues-java.md)
- [Java에서 Azure Table Service 시작](../../cosmos-db/table-storage-how-to-use-java.md)
- [Java에서 Azure File Service 시작](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>다음 단계

다른 언어용 샘플에 대한 정보:

- .NET: [.NET을 사용한 Azure Storage 샘플](storage-samples-dotnet.md)
- Python: [Python을 사용한 Azure Storage 샘플](storage-samples-python.md)
- JavaScript/node.js: [JavaScript를 사용한 Azure Storage 샘플](storage-samples-javascript.md)
- C++: [C++를 사용한 Azure Storage 샘플](storage-samples-c-plus-plus.md)
- 모든 기타 언어: [Azure Storage 샘플](storage-samples.md)
