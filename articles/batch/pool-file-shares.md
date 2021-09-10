---
title: Azure Batch 풀용 Azure 파일 공유
description: Azure Batch의 Linux 또는 Windows 풀에서 컴퓨팅 노드의 Azure Files 공유를 탑재하는 방법입니다.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: 2c5cbf1a3e5d74927ddc74c4838c31f68a348876
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772117"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>배치 풀에서 Azure 파일 공유 사용

[Azure Files](../storage/files/storage-files-introduction.md)는 SMB(서버 메시지 블록) 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. Batch 풀 컴퓨팅 노드에서 Azure 파일 공유를 탑재하여 사용할 수 있습니다.

## <a name="considerations-for-use-with-batch"></a>배치와 함께 사용 시 고려 사항

프리미엄이 아닌 Azure Files를 사용 중인 경우 비교적 적은 병렬 수의 작업을 실행하는 풀이 있을 때 Azure 파일 공유를 사용하는 것이 좋습니다. [성능 및 크기 조정 대상](../storage/files/storage-files-scale-targets.md)을 검토하여 예상 풀 크기 및 자산 파일 수를 고려할 경우 Azure Files(Azure Storage 계정 사용)를 사용해야 하는지 결정합니다.

Azure 파일 공유는 [비용 효율적](https://azure.microsoft.com/pricing/details/storage/files/)이며 다른 지역에 대한 데이터 복제를 사용하여 구성할 수 있으므로 전역으로 중복됩니다.

온-프레미스 컴퓨터에서 Azure 파일 공유를 동시에 탑재할 수 있습니다. 그러나 특히 REST API를 사용할 때 [동시성 영향](../storage/blobs/concurrency-manage.md)을 이해해야 합니다.

Azure 파일 공유에 대한 일반적인 [플랜 고려 사항](../storage/files/storage-files-planning.md)도 참조하세요.

## <a name="create-a-file-share"></a>파일 공유 만들기

배치 계정에 연결된 스토리지 계정 또는 별도의 스토리지 계정으로 Azure 파일 공유를 만들 수 있습니다. 자세한 내용은 [Azure 파일 공유 만들기](../storage/files/storage-how-to-create-file-share.md)를 참조하세요.

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Batch 풀에 Azure 파일 공유 탑재

풀에 Azure 파일 공유를 탑재하는 방법에 대한 자세한 내용은 [Batch 풀에서 가상 파일 시스템 탑재](virtual-file-mount.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Batch에서 데이터를 읽고 쓰기 위한 다른 옵션에 대한 내용은 [작업 및 태스크 출력 유지](batch-task-output.md)를 참조하세요.
- Batch 컨테이너 워크로드에 대한 파일 시스템을 배포하는 [Shipyard 레시피](https://github.com/Azure/batch-shipyard/tree/master/recipes)가 포함된 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 도구 키트를 참조하세요.
