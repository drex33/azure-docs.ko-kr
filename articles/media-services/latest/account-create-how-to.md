---
title: Azure Media Services 계정 만들기
description: 이 자습서에서는 Azure Media Services 계정을 만드는 단계를 안내합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/29/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 16f6e5900b7454019a6a35a041cf683dfbb63609
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133544557"
---
# <a name="create-a-media-services-account"></a>Media Services 계정 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure에서 암호화, 인코딩, 분석, 관리 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정은 하나 이상의 스토리지 계정과 연결되어야 합니다. 이 문서에서는 새로운 Azure Media Services 계정을 만드는 단계를 설명합니다.

[!INCLUDE [note 2020-05-01 API](./includes/note-2020-05-01-account-creation.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure 관리 ID 플랫폼에 익숙하지 않은 경우 플랫폼과 ID 유형 간의 차이점을 이해하는 데 시간이 걸릴 수 있습니다.  Media Services 계정 기본 관리 ID 유형은 사용자 관리 ID입니다.

- [Microsoft ID 플랫폼](../../active-directory/develop/app-objects-and-service-principals.md)대해 읽어 읽습니다. 
- Azure [리소스에 대한 관리 ID에 대해 읽어 읽습니다.](../../active-directory/managed-identities-azure-resources/overview.md)
- [애플리케이션 및 서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)에 대해 잠시 읽어보는 것이 좋을 수도 있습니다.

## <a name="create-an-account"></a>계정 만들기
 
Azure Portal 또는 CLI를 사용하여 Media Services 계정을 만들 수 있습니다. 사용하려는 방법에 대한 탭을 선택합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!-- NOTE: The following are in the includes folder and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

## <a name="portal"></a>[포털](#tab/portal/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

[!INCLUDE [enable a system-assigned managed identity](./includes/task-create-media-services-system-managed-identity.md)]

[!INCLUDE [add system assigned managed identity in the portal](./includes/task-storage-system-managed-identity-portal.md)]

[!INCLUDE [add encryption to media services account](./includes/task-security-encryption-managed-identity-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

## <a name="set-the-azure-subscription"></a>Azure 구독 설정

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다. Media Services에서 스토리지 계정을 사용하는 방법에 대한 자세한 내용은 [스토리지 계정](storage-account-concept.md)을 참조하세요.

**기본** 스토리지 계정이 하나 있어야 하며 미디어 서비스 계정과 연결된 **보조** 스토리지 계정이 여러 개 있을 수 있습니다. Media Services는 **범용 v2**(GPv2) 또는 **범용 v1**(GPv1) 계정을 지원합니다. Blob 전용 계정은 **기본** 으로 허용되지 않습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 옵션](../../storage/common/storage-account-overview.md)을 참조하세요.

다음 예제에서는 범용 v2, 표준 LRS 계정을 만듭니다. 스토리지 계정을 실험하려면 `--sku Standard_LRS`를 사용합니다. 그러나 프로덕션용 SKU를 선택하는 경우 비즈니스 연속성을 위해 지리적 복제를 제공하는 `--sku Standard_RAGRS`를 고려해야 합니다. 자세한 내용은 [스토리지 계정](/cli/azure/storage/account)을 참조하세요.

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Media Services 계정 만들기

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

---
