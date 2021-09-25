---
title: 데이터 전송을 위한 Azure 솔루션 선택 | Microsoft Docs
description: 사용자 환경에서 사용 가능한 네트워크 대역폭 및 데이터 크기에 따라 데이터 전송을 위한 Azure 솔루션을 선택하는 방법을 알아봅니다.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: c3765114c73be73beba6d898385077b83d46e3bc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638295"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>데이터 전송을 위한 Azure 솔루션 선택

이 문서에서는 몇 가지 일반적인 Azure 데이터 전송 솔루션에 대해 간략하게 설명합니다. 사용자 환경의 네트워크 대역폭 및 전송하려는 데이터 크기에 따라 권장되는 옵션의 링크도 제공됩니다.

## <a name="types-of-data-movement"></a>데이터 이동 유형

데이터 전송은 오프라인이나 네트워크 연결을 통해 이루어질 수 있습니다. 다음과 같은 조건에 따라 솔루션을 선택합니다.

- **데이터 크기** - 전송할 데이터 크기
- **전송 빈도** - 일회성 또는 정기적 데이터 수집
- **네트워크** - 사용자 환경에서 데이터 전송에 사용 가능한 대역폭

데이터 이동 유형은 다음과 같을 수 있습니다.

- **배송 가능한 디바이스를 사용한 오프라인 전송** - 오프라인 일회성 대량 데이터 전송을 수행하려는 경우 배송 가능한 물리적 디바이스를 사용합니다. Microsoft에서 디스크 또는 안전한 특수 디바이스를 보내 드립니다. 또는 사용자 고유의 디스크를 구매하여 배송할 수도 있습니다. 데이터를 디바이스에 복사한 다음, 데이터가 업로드되는 Azure로 배송합니다.  이 경우 사용 가능한 옵션은 Data Box Disk, Data Box, Data Box Heavy 및 Import/Export(사용자 고유의 디스크 사용)입니다.

- **네트워크 전송** - 네트워크 연결을 통해 데이터를 Azure로 전송합니다. 이 작업은 여러 가지 방법으로 수행할 수 있습니다.

  - **그래픽 인터페이스** - 때때로 몇 개의 파일만 전송하며 데이터 전송을 자동화하지 않아도 되는 경우 Azure Portal에서 Azure Storage Explorer, 웹 기반 탐색 도구 등의 그래픽 인터페이스 도구를 선택할 수 있습니다.
  - **스크립팅 또는 프로그래밍 방식 전송** - Microsoft에서 제공하는 최적화된 소프트웨어 도구를 사용하거나 REST API/SDK를 직접 호출할 수 있습니다. 사용 가능한 스크립팅 가능 도구는 AzCopy, Azure PowerShell 및 Azure CLI입니다. 프로그래밍 인터페이스의 경우 .NET, Java, Python, Node/JS, C++, Go, PHP 또는 Ruby용 SDK 중 하나를 사용합니다.
  - **온-프레미스 디바이스** - 데이터 센터에 상주하며 네트워크를 통해 데이터 전송을 최적화하는 물리적 또는 가상 디바이스를 제공합니다. 이 디바이스는 자주 사용하는 파일의 로컬 캐시도 제공합니다. 물리적 디바이스는 Azure Stack Edge이고, 가상 디바이스는 Data Box Gateway입니다. 둘 다 온-프레미스에서 영구적으로 실행되며, 네트워크를 통해 Azure에 연결됩니다.
  - **관리되는 데이터 파이프라인** - 여러 Azure 서비스, 온-프레미스 또는 둘 다의 조합 간에 정기적으로 파일을 전송하려면 클라우드 파이프라인을 설정할 수 있습니다. Azure Data Factory를 사용하여 데이터 파이프라인을 설정 및 관리하고, 분석을 위해 데이터를 이동 및 변환합니다.

다음 시각적 개체는 전송에 사용 가능한 네트워크 대역폭, 전송하려는 데이터 크기 및 전송 빈도에 따라 다양한 Azure 데이터 전송 도구를 선택하는 지침을 보여 줍니다.

![Azure 데이터 전송 도구](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**한 디바이스 유형의 주문을 여러 개 넣어 오프라인 전송 디바이스(Data Box Disk, Data Box 및 Data Box Heavy)의 상한을 확장할 수 있습니다.*

## <a name="selecting-a-data-transfer-solution"></a>데이터 전송 솔루션 선택

다음 질문에 답하면 데이터 전송 솔루션을 선택하는 데 도움이 됩니다.

- 사용 가능한 네트워크 대역폭이 제한적이거나 없으며 대용량 데이터 세트를 전송하려고 하나요?

    그렇다면 [시나리오 1: 네트워크 대역폭이 없거나 제한적인 경우 대용량 데이터 세트 전송](storage-solution-large-dataset-low-network.md)을 참조하세요.
- 보통-높은 네트워크 대역폭이 있으며 네트워크를 통해 대용량 데이터 세트를 전송하려고 하나요?

    그렇다면 [시나리오 2: 보통-높은 네트워크 대역폭에서 대용량 데이터 세트 전송](storage-solution-large-dataset-moderate-high-network.md)을 참조하세요.
- 네트워크를 통해 몇 개의 파일만 때때로 전송하려고 하나요?

    그렇다면 [시나리오 3: 제한적-보통 네트워크 대역폭에서 작은 데이터 세트 전송](storage-solution-small-dataset-low-moderate-network.md)을 참조하세요.
- 일정한 간격마다 수행되는 지정 시간 데이터 전송을 원하시나요?

    그렇다면 [시나리오 4: 정기적 데이터 전송](storage-solution-periodic-data-transfer.md)에 요약된 스크립팅/프로그래밍 방식 옵션을 사용하세요.
- 지속적인 데이터 전송을 원하시나요?

    그렇다면 [시나리오 4: 정기적 데이터 전송](storage-solution-periodic-data-transfer.md)의 옵션을 사용하세요.

## <a name="data-transfer-feature-in-azure-portal"></a>Azure Portal의 데이터 전송 기능

Azure Portal에서 Azure Storage 계정으로 이동하여 **데이터 전송** 기능을 선택할 수도 있습니다. 환경에서 네트워크 대역폭, 전송할 데이터의 크기, 데이터 전송 빈도를 제공합니다. 제공된 정보에 해당하는 최적의 데이터 전송 솔루션이 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage Explorer 소개](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [AzCopy의 개요 읽기](./storage-use-azcopy-v10.md)
- [빠른 시작: PowerShell을 사용하여 Blob 업로드, 다운로드 및 나열](../blobs/storage-quickstart-blobs-powershell.md)
- [빠른 시작: Azure CLI를 사용하여 Blob 생성, 다운로드 및 나열](../blobs/storage-quickstart-blobs-cli.md)
- 자세한 정보:

  - [오프라인 전송을 위한 Azure Data Box, Azure Data Box Disk 및 Azure Data Box Heavy](../../databox/index.yml)
  - [온라인 전송을 위한 Azure Data Box Gateway와 Azure Stack Edge](../../databox-online/index.yml)
- [Azure Data Factory 알아보기](../../data-factory/copy-activity-overview.md).
- REST API를 사용하여 데이터 전송

  - [.NET](/dotnet/api/overview/azure/storage)
  - [Java에서](/java/api/overview/azure/storage)
