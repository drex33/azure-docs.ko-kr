---
title: Azure Portal에서 스토리지 계정에 대한 처리량 및 대기 시간 메트릭 확인 | Microsoft Docs
description: 포털에서 스토리지 계정에 대한 처리량 및 대기 시간 메트릭을 확인하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ced5b6493753baab64f5390b7dee815227334796
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549520"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>스토리지 계정에 대한 처리량 및 대기 시간 메트릭 확인

이 자습서는 4부로, 시리즈의 마지막 부분입니다. 이전 자습서에서는 Azure Storage 계정에 대용량의 임의 데이터를 업로드 및 다운로드하는 방법을 알아보았습니다. 이 자습서에서는 메트릭을 사용하여 Azure Portal에서 처리량과 대기 시간을 보는 방법을 보여 줍니다.

시리즈 4부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - Azure Portal에서 차트 구성
> - 처리량 및 대기 시간 메트릭 확인

[Azure Storage 메트릭](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)은 Azure Monitor를 사용하여 스토리지 계정의 성능 및 가용성에 대한 통합된 뷰를 제공합니다.

## <a name="configure-metrics"></a>메트릭 구성

스토리지 계정의 **설정** 아래에 있는 **메트릭** 으로 이동합니다.

**하위 서비스** 드롭다운에서 Blob을 선택합니다.

**메트릭** 에서 다음 표에 있는 메트릭 중 하나를 선택합니다.

다음 메트릭은 애플리케이션의 대기 시간 및 처리량에 대한 아이디어를 제공합니다. 포털에서 구성하는 메트릭은 1분 평균 단위입니다. 트랜잭션이 1분의 중간에 완료되면 해당 분 데이터는 평균을 위해 반감됩니다. 애플리케이션에서 업로드 및 다운로드 작업은 시간이 측정되고 파일을 업로드 및 다운로드하는 데 걸린 실제 시간을 제공했습니다. 이 정보는 Portal 메트릭과 함께 사용하여 처리량을 완전히 이해할 수 있습니다.

|메트릭|정의|
|---|---|
|**성공 E2E 대기 시간**|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|
|**성공 서버 대기 시간**|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다. |
|**트랜잭션**|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 이 예제에서 블록 크기는 100MB로 설정되었습니다. 이 경우 각100MB 블록은 트랜잭션으로 간주합니다.|
|**수신**|수신 데이터 양입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다. |
|**송신**|송신 데이터 양입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다. |

**시간** 옆의 **최근 24시간(자동)** 을 선택합니다. **시간 단위** 에 대해 **지난 1시간** 및 **분** 을 선택하고 **적용** 을 클릭합니다.

![스토리지 계정 메트릭](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

차트에는 둘 이상의 메트릭을 지정할 수 있지만 둘 이상의 메트릭을 지정하면 차원별로 그룹화할 수 없습니다.

## <a name="dimensions"></a>차원

[차원](./monitor-blob-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions)은 차트를 자세히 보고 자세한 정보를 얻는 데 사용됩니다. 메트릭에 따라 서로 다른 차원이 있습니다. 사용 가능한 하나의 차원은 **API 이름** 차원입니다. 이 차원은 차트를 각각 별도의 API 호출로 나눕니다. 아래의 첫 번째 이미지는 스토리지 계정에 대한 총 트랜잭션의 예제 차트를 보여 줍니다. 두 번째 이미지는 동일한 차트를 보여 주지만 API 이름 차원이 선택되어 있습니다. 확인한 대로 각 트랜잭션이 나열되고 API 이름에 의해 생성된 호출 수에 대한 자세한 정보를 제공합니다.

![스토리지 계정 메트릭 - 차원이 없는 트랜잭션](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![스토리지 계정 메트릭 - 트랜잭션](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않을 때 리소스 그룹, 가상 머신 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 VM에 대한 리소스 그룹을 선택하고 [삭제]를 클릭합니다.

## <a name="next-steps"></a>다음 단계

시리즈의 4부에서는 다음 방법을 비롯하여 예제 솔루션에 대한 메트릭을 보는 방법을 알아보았습니다.

> [!div class="checklist"]
> - Azure Portal에서 차트 구성
> - 처리량 및 대기 시간 메트릭 확인

미리 작성된 스토리지 샘플을 보려면 이 링크를 따라 이동합니다.

> [!div class="nextstepaction"]
> [Azure Storage 스크립트 샘플](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md
