---
title: 고객 관리형 키 또는 BYOK 포털 사용
description: 이 자습서에서는 Azure Portal을 사용하여 고객 관리형 키를 사용하도록 설정하거나 Azure Media Services 스토리지 계정을 통해 BYOK(Bring Your Own Key)를 사용하도록 설정합니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 1a5b4c8e387085b8fee6c6e82ae765a186f43e87
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356669"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>자습서: Azure Portal을 사용하여 Media Services에서 고객 관리형 키 또는 BYOK 사용

2020-05-01 이상 버전의 API를 사용하면 시스템 관리 ID가 있는 Azure Media Services 계정에서 고객 관리형 RSA 키를 사용할 수 있습니다. 이 자습서에서는 Azure Portal의 단계를 다룹니다.

사용되는 서비스는 다음과 같습니다.

- Azure Storage
- Azure Key Vault
- Azure Media Services

이 자습서에서는 Azure Portal을 사용하여 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> - 리소스 그룹을 만듭니다.
> - 시스템 관리 ID를 사용하여 스토리지 계정을 만듭니다.
> - 시스템 관리 ID를 사용하여 Media Services 계정 만들기
> - 고객 관리형 RSA 키를 저장하기 위한 키 자격 증명 모음 만들기

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="system-managed-keys"></a>시스템 관리형 키

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> 다음 스토리지 계정 만들기 단계에서는 고급 설정에서 시스템 관리형 키 선택을 선택합니다.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> 다음 스토리지 암호화 단계에서는 **고객 관리형 키 선택** 을 선택합니다.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>키 변경

키가 변경되면 Media Services에서 자동으로 감지합니다. 선택 사항: 이 프로세스를 테스트하려면 동일한 키에 대해 다른 키 버전을 만듭니다. Media Services는 키가 변경되었음을 감지해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

여기서 만든 리소스가 더 이상 필요 없고 *리소스 요금이 더 이상 청구되지 않도록 하려면* 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

방법을 알아보려면 다음 문서로 이동하세요.
> [!div class="nextstepaction"]
> [URL에 따라 원격 파일을 인코딩하고 REST를 사용하여 비디오 스트리밍](stream-files-tutorial-with-rest.md)
