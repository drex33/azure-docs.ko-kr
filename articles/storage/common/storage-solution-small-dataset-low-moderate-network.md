---
title: 낮음-보통 네트워크 대역폭에서 작은 데이터 세트를 위한 Azure 데이터 전송 옵션 | Microsoft Docs
description: 사용자 환경의 네트워크 대역폭이 낮음-보통 수준이며 작은 데이터 세트를 전송하려는 경우 데이터 전송을 위한 Azure 솔루션을 선택하는 방법을 알아봅니다.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f5c20e8c026e81ab0aae5e648142a7c94ea8835a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588621"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>낮음-보통 네트워크 대역폭에서 작은 데이터 세트의 데이터 전송

이 문서에서는 사용자 환경의 네트워크 대역폭이 낮음-보통 수준이며 작은 데이터 세트를 전송하려는 경우의 데이터 전송 솔루션에 대해 간략하게 설명합니다. 이 시나리오에서 권장되는 데이터 전송 옵션 및 각각의 주요 기능 매트릭스에 대해서도 설명합니다.

사용 가능한 모든 데이터 전송 옵션에 대한 개요를 보려면 [Azure 데이터 전송 솔루션 선택](storage-choose-data-transfer-solution.md)으로 이동하세요.

## <a name="scenario-description"></a>시나리오 설명

작은 데이터 세트는 GB에서 TB 단위의 데이터 크기를 가리킵니다. 낮음-보통 네트워크 대역폭은 45Mbps(데이터 센터의 T3 연결)~1Gbps를 가리킵니다.

- 소수의 파일만 전송하고 데이터 전송을 자동화할 필요가 없는 경우 그래픽 인터페이스가 있는 도구를 고려해 보십시오.
- 시스템 관리에 익숙한 경우 명령줄이나 프로그래밍 방식/스크립팅 도구를 사용하는 것이 좋습니다.

## <a name="recommended-options"></a>권장 옵션

이 시나리오에서 권장되는 옵션은 다음과 같습니다.

- Azure Portal의 Azure Storage Explorer 및 Azure Storage와 같은 **그래픽 인터페이스 도구**. 이러한 도구를 통해 데이터를 쉽게 확인하고 몇 개의 파일을 빠르게 전송할 수 있습니다.

  - **Azure Storage Explorer** - 이 플랫폼 간 도구를 통해 Azure Storage 계정의 내용을 관리할 수 있습니다. BLOB, 파일, 큐, 테이블 및 Azure Cosmos DB 엔터티를 업로드, 다운로드 및 관리할 수 있습니다. Blob Storage에서 사용하여 Blob 및 폴더를 관리하고, 로컬 파일 시스템과 Blob Storage 간에 또는 스토리지 계정 간에 Blob을 업로드 및 다운로드할 수 있습니다.
  - **Azure Portal** - Azure Portal의 Azure Storage는 파일을 검색하고 새 파일을 한 번에 하나씩 업로드하는 웹 기반 인터페이스를 제공합니다. 파일을 빠르게 탐색하거나 많은 새 파일을 간편하게 업로드하기 위해 도구를 설치하거나 명령을 실행하는 방식을 원치 않을 경우에 유용한 옵션입니다.

- AzCopy/PowerShell/Azure CLI 및 Azure Storage REST API와 같은 **스크립팅/프로그래밍 방식 도구**.

  - **AzCopy** - 이 명령줄 도구를 사용하여 Azure Blob, Files 및 Table Storage에서 최적 성능으로 데이터를 쉽게 복사할 수 있습니다. AzCopy는 동시성 및 병렬 처리 기능과 중단된 복사 작업을 다시 시작하는 기능을 지원합니다.
  - **Azure PowerShell** - 시스템 관리에 익숙한 사용자의 경우 Azure PowerShell에서 Azure Storage 모듈을 사용하여 데이터를 전송합니다.
  - **Azure CLI** - 이 플랫폼 간 도구를 사용하여 Azure 서비스를 관리하고 Azure Storage에 데이터를 업로드할 수 있습니다.
  - **Azure Storage REST API/SDK** - 애플리케이션을 빌드할 때 Azure Storage REST API/SDK에 대해 애플리케이션을 개발하고 여러 언어로 제공되는 Azure 클라이언트 라이브러리를 사용할 수 있습니다.

## <a name="comparison-of-key-capabilities"></a>주요 기능 비교

다음 표에서는 주요 기능의 차이점을 요약해서 보여 줍니다.

| 특징 | Azure Storage Explorer | Azure Portal | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure Storage REST API 또는 SDK |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| 가용성 | 다운로드 및 설치 <br>독립 실행형 도구 | Azure Portal의 웹 기반 탐색 도구 | 명령줄 도구 |.NET, Java, Python, JavaScript, C++, Go, Ruby 및 PHP의 프로그래밍 가능한 인터페이스 |
| 그래픽 인터페이스 | 예 | 예 | 아니요 | 아니요 |
| 지원되는 플랫폼 | Windows, Mac, Linux | 웹 기반 |Windows, Mac, Linux |모든 플랫폼 |
| Blob 및 폴더에 대해 허용되는 Blob<br>스토리지 작업 | 업로드<br>다운로드<br>관리 | 업로드<br>다운로드<br>관리 |업로드<br>다운로드<br>관리 | 예, 사용자 지정 가능 |
| 파일 및 폴더에 대해 허용되는 Data Lake Gen1<br>스토리지 작업 | 업로드<br>다운로드<br>관리 | No |업로드<br>다운로드<br>관리                   | No |
| 파일 및 디렉터리에 대해 허용되는 File<br>Storage 작업 | 업로드<br>다운로드<br>관리 | 업로드<br>다운로드<br>관리   |업로드<br>다운로드<br>관리 | 예, 사용자 지정 가능 |
| 테이블에 대해 허용되는 Table<br>Storage 작업 |관리 | No |AzCopy v7의 테이블 지원 |예, 사용자 지정 가능|
| 허용되는 Queue Storage | 관리 | 아니요  |아니요 | 예, 사용자 지정 가능|

## <a name="next-steps"></a>다음 단계

- [Azure Storage Explorer를 사용하여 데이터를 전송](/azure/architecture/data-science-process/move-data-to-azure-blob-using-azure-storage-explorer)하는 방법을 알아봅니다.
- [AzCopy를 사용하여 데이터 전송](./storage-use-azcopy-v10.md)
