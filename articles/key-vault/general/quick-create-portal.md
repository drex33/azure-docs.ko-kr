---
title: 빠른 시작 - Azure Portal을 사용하여 Azure Key Vault 만들기
description: Azure Portal을 사용하여 Azure Key Vault를 만드는 방법을 보여주는 빠른 시작
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.custom: mvc, mode-portal
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: 19c7371cfbf662a58a51b6e97d6f599971ecffb8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133048089"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 키 자격 증명 모음 만들기

Azure Key Vault는 [키](../keys/index.yml), [비밀](../secrets/index.yml) 및 [인증서](../certificates/index.yml)에 대한 보안 저장소를 제공하는 클라우드 서비스입니다. Key Vault에 대한 자세한 내용은 [Azure Key Vault 정보](overview.md)를 참조하세요. 키 자격 증명 모음에 저장할 수 있는 항목에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](about-keys-secrets-certificates.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

이 빠른 시작에서는 [Azure Portal](https://portal.azure.com)을 사용하여 키 자격 증명 모음을 만듭니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vault"></a>자격 증명 모음 만들기

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.
2. 검색 상자에 **Key Vault** 를 입력합니다.
3. 결과 목록에서 **Key Vault** 를 선택합니다.
4. Key Vault 섹션에서 **만들기** 를 선택합니다.
5. **Key Vault 만들기** 섹션에서 다음 정보를 제공합니다.
    - **Name**: 고유 이름은 필수입니다. 이 빠른 시작의 경우 **Contoso-vault2** 를 사용합니다. 
    - **구독**: 구독을 선택합니다.
    - **리소스 그룹** 에서 **새로 만들기** 를 선택하고 리소스 그룹 이름을 입력합니다.
    - **위치** 풀 다운 메뉴에서 위치를 선택합니다.
    - 다른 옵션은 기본값으로 그대로 둡니다.
6. 위의 정보를 제공한 후 **만들기** 를 선택합니다.

아래에 나열된 두 개의 속성을 기록합니다.

* **자격 증명 모음 이름**: 이 예에서는 **Contoso-Vault2** 입니다. 다른 단계에서 이 이름을 사용합니다.
* **자격 증명 모음 URI**: 이 예에서는 `https://contoso-vault2.vault.azure.net/` 입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 애플리케이션은 URI를 사용해야 합니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 작업을 수행할 권한이 있는 유일한 계정입니다.

:::image type="content" source="../media/quick-create-portal/vault-properties.png" alt-text="Key Vault 만들기 완료 후 출력":::

## <a name="clean-up-resources"></a>리소스 정리

다른 Key Vault 빠른 시작과 자습서는 이 빠른 시작을 기반으로 빌드됩니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.
더 이상 필요 없으면 리소스 그룹을 삭제하고 Key Vault 및 관련 리소스를 삭제합니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제** 를 선택합니다.
3. **리소스 그룹 이름 입력** 상자에 리소스 그룹 이름을 입력하고 **삭제** 를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal을 사용하여 Key Vault를 만들었습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](overview.md) 참조
- [Azure Key Vault 보안 개요](security-features.md)를 검토하세요.
- [Azure Key Vault 개발자 가이드](developers-guide.md) 참조
