---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/19/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0914cf9515930e23e4134181ffe8332e36eacffe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612932"
---
Azure Blob Storage는 클라우드를 위한 Microsoft의 개체 스토리지 솔루션입니다. Blob Storage는 구조화되지 않은 대량의 데이터를 저장하는 데 최적화되어 있습니다. 비정형 데이터는 텍스트 또는 이진 데이터와 같은 특정 데이터 모델이나 정의를 따르지 않는 데이터입니다.

## <a name="about-blob-storage"></a>Blob Storage 정보

Blob Storage는 다음을 위해 설계되었습니다.

* 브라우저에 이미지 또는 문서 직접 제공
* 분산 액세스용 파일 저장.
* 비디오 및 오디오 스트리밍.
* 로그 파일에 쓰기
* 백업/복원, 재해 복구 및 보관용 데이터 저장
* 온-프레미스 또는 Azure 호스팅 서비스에서 분석하기 위한 데이터 저장.

사용자 또는 클라이언트 애플리케이션은 전 세계 어디서든 HTTP/HTTPS를 통해 Blob Storage의 개체에 액세스할 수 있습니다. Blob Storage의 개체는 [Azure Storage REST API](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](/powershell/module/az.storage), [Azure CLI](/cli/azure/storage) 또는 Azure Storage 클라이언트 라이브러리를 통해 액세스할 수 있습니다. 클라이언트 라이브러리는 다음을 포함하여 다양한 언어에서 사용할 수 있습니다.

* [.NET](/dotnet/api/overview/azure/storage)
* [Java](/java/api/overview/azure/storage)
* [Node.JS](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 정보

Blob Storage는 Microsoft의 클라우드용 엔터프라이즈 빅 데이터 분석 솔루션인 Azure Data Lake Storage Gen2를 지원합니다. Azure Data Lake Storage Gen2는 다음을 비롯한 Blob 스토리지의 장점뿐만 아니라 계층적 파일 시스템을 제공합니다.

* 저렴한 비용의 계층화된 스토리지
* 고가용성
* 강력한 일관성
* 재해 복구 기능

Azure Data Lake Storage Gen2에 대한 자세한 내용은 [Azure Data Lake Storage Gen2 소개](../articles/storage/blobs/data-lake-storage-introduction.md)를 참조하세요.