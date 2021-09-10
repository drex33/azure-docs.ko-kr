---
title: 컨테이너에 대한 일시 삭제
titleSuffix: Azure Storage
description: 컨테이너에 대한 일시 삭제는 애플리케이션 또는 다른 스토리지 계정 사용자에 의해 잘못 수정되거나 삭제될 때 데이터를 보다 쉽게 복구할 수 있도록 데이터를 보호합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: d3684d3fcb79fca31f403294a640887fa8e33d92
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113504480"
---
# <a name="soft-delete-for-containers"></a>컨테이너에 대한 일시 삭제

컨테이너 일시 삭제는 지정된 기간 동안 시스템에서 삭제된 데이터를 유지 관리하여 데이터가 실수로 삭제되지 않도록 보호합니다. 보존 기간 동안 일시 삭제된 컨테이너와 해당 콘텐츠를 삭제 당시의 컨테이너 상태로 복원할 수 있습니다. 보존 기간이 만료되면 컨테이너와 해당 콘텐츠가 영구적으로 삭제됩니다.

## <a name="recommended-data-protection-configuration"></a>권장되는 데이터 보호 구성

Blob 일시 삭제는 Blob 데이터에 대한 포괄적인 데이터 보호 전략의 일환입니다. Blob 데이터에 대한 최적의 보호를 위해 다음과 같은 모든 데이터 보호 기능을 사용하는 것이 좋습니다.

- 컨테이너 일시 삭제는 삭제된 컨테이너를 복원합니다. 컨테이너 일시 삭제를 사용하도록 설정하는 방법을 알아보려면 [컨테이너에 대한 일시 삭제 사용 및 관리](soft-delete-container-enable.md)를 참조하세요.
- Blob 버전 관리는 Blob의 이전 버전을 자동으로 유지합니다. Blob 버전 관리를 사용하는 경우 데이터를 잘못 수정하거나 삭제한 경우 이전 버전의 Blob을 복원하여 데이터를 복구할 수 있습니다. Blob 버전 관리를 사용하도록 설정하는 방법을 알아보려면 [Blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조하세요.
- Blob 일시 삭제 - 삭제된 Blob, 스냅샷 또는 버전을 복원합니다. Blob 일시 삭제를 사용하도록 설정하는 방법을 알아보려면 [Blob에 대한 일시 삭제 사용 및 관리](soft-delete-blob-enable.md)를 참조하세요.

Microsoft의 데이터 보호 권장 사항에 대해 자세히 알아보려면 [데이터 보호 개요](data-protection-overview.md)를 참조하세요.

## <a name="how-container-soft-delete-works"></a>컨테이너 일시 삭제 작동 방식

컨테이너 일시 삭제를 사용하는 경우 삭제된 컨테이너에 대한 보존 기간을 1일에서 365일 사이로 지정할 수 있습니다. 기본 보존 기간은 7일입니다. 보존 기간 중에는 **컨테이너 복원** 작업을 호출하여 삭제된 컨테이너를 복구할 수 있습니다.

컨테이너를 복원하면 컨테이너의 Blob과 모든 Blob 버전 및 스냅샷도 복원됩니다. 하지만 컨테이너 자체가 삭제된 경우에만 컨테이너 일시 삭제를 사용하여 Blob을 복원할 수 있습니다. 부모 컨테이너가 삭제되지 않았을 때 삭제된 Blob을 복원하려면 Blob 일시 삭제 또는 Blob 버전 관리를 사용해야 합니다.

> [!WARNING]
> 컨테이너 일시 삭제는 삭제 시 전체 컨테이너와 그 콘텐츠만 복원할 수 있습니다. 컨테이너 일시 삭제를 사용하여 컨테이너 내의 삭제된 Blob를 복원할 수는 없습니다. 또한 Blob 일시 삭제 및 Blob 버전 관리를 사용하여 컨테이너의 개별 Blob을 보호하는 것이 좋습니다.
>
> 컨테이너를 복원할 경우에는 원래 이름으로 복원해야 합니다. 원래 이름을 사용하여 새 컨테이너를 만든 경우 일시 삭제된 컨테이너를 복원할 수 없습니다.

다음 다이어그램은 컨테이너 일시 삭제를 사용하는 경우 삭제된 컨테이너가 어떻게 복원되는지 보여줍니다.

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="일시 삭제된 컨테이너가 복원되는 방법을 보여주는 다이어그램":::

보존 기간이 만료되면 컨테이너는 Azure Storage에서 영구적으로 삭제되며 복구할 수 없습니다. 컨테이너가 삭제된 시점부터 보존 기간 클록이 시작됩니다. 언제든 보존 기간을 변경할 수 있지만 업데이트된 보존 기간은 새로 삭제하는 컨테이너에만 적용됩니다. 이전에 삭제된 컨테이너는 컨테이너가 삭제된 시점의 보존 기간을 기준으로 영구 삭제됩니다.

컨테이너 일시 삭제를 사용하지 않도록 설정하면 이전에 일시 삭제되었던 컨테이너가 영구 삭제됩니다. 모든 일시 삭제된 컨테이너는 삭제되었을 때의 보존 기간이 만료되면 영구 삭제됩니다.

컨테이너 일시 삭제는 다음 유형의 스토리지 계정에서 사용할 수 있습니다.

- 범용 v2 및 v1 스토리지 계정
- 블록 Blob Storage 계정
- Blob Storage 계정

Azure Data Lake Storage Gen2에서 사용하도록 설정된 계층 구조 네임스페이스를 사용하는 스토리지 계정 또한 지원됩니다.

Azure Storage REST API 버전 2019-12-12 이상에서는 컨테이너 일시 삭제를 지원합니다.

> [!IMPORTANT]
> 컨테이너 일시 삭제는 스토리지 계정 삭제에 대해 보호하는 것이 아니라 해당 계정의 컨테이너 삭제에 대해서만 보호합니다. 스토리지 계정이 삭제되지 않도록 보호하려면 스토리지 계정 리소스에 대한 잠금을 구성합니다. Azure Resource Manager 리소스 잠금에 대한 자세한 내용은 [예기치 않은 변경을 방지하기 위한 리소스 잠금](../../azure-resource-manager/management/lock-resources.md)을 참조하세요.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

컨테이너 일시 삭제 사용에 대한 추가 요금은 없습니다. 일시 삭제된 컨테이너의 데이터는 활성 데이터와 동일한 요율로 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

- [컨테이너 일시 삭제 구성](soft-delete-container-enable.md)
- [Blob에 대한 일시 삭제](soft-delete-blob-overview.md)
- [Blob 버전 관리](versioning-overview.md)
