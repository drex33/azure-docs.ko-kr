---
title: 파이프라인 작업에서 Azure Key Vault 비밀 사용
description: Azure Key Vault에서 저장된 자격 증명을 가져와서 데이터 팩터리 파이프라인 실행 중에 사용하는 방법을 알아봅니다.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 09a2c057cbefefc02e40d326eea094e37b092cc5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851157"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>파이프라인 작업에서 Azure Key Vault 비밀 사용

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Key Vault에 자격 증명 또는 비밀 값을 저장하고 파이프라인 실행 중에 사용하여 활동에 전달할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 기능은 데이터 팩터리 관리 ID를 사용합니다.  이 기능이 [데이터 팩터리용 관리 ID](./data-factory-service-identity.md)에서 작동하는 방식과 데이터 팩터리에 연결된 관리 ID가 있는지 확인하는 방법을 알아봅니다.

## <a name="steps"></a>단계

1. 데이터 팩터리의 속성을 열고 관리 ID 애플리케이션 ID 값을 복사합니다.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png" alt-text="관리 ID 값":::

2. 키 자격 증명 모음 액세스 정책을 열고 비밀을 가져오고 나열할 수 있는 관리 ID 권한을 추가합니다.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png" alt-text="액세스 정책 추가 작업이 강조 표시된 액세스 정책 페이지를 보여주는 &quot; &quot; &quot; &quot; 스크린샷.":::

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png" alt-text="Key Vault 액세스 정책":::

    **추가** 를 클릭한 다음 **저장** 을 클릭합니다.

3. 키 자격 증명 모음 비밀로 이동하여 비밀 식별자를 복사합니다.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png" alt-text="비밀 식별자":::

    Data Factory 파이프라인을 실행하는 동안 가져오려는 비밀 URI를 적어 둡니다.

4. Data Factory 파이프라인에서 새 웹 작업을 추가하고 다음과 같이 구성합니다.  

    |속성  |값  |
    |---------|---------|
    |출력 보안 설정     |True         |
    |URL     |[비밀 URI 값]?api-version=7.0         |
    |메서드     |GET         |
    |인증     |MSI         |
    |리소스        |https://vault.azure.net       |

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png" alt-text="웹 작업":::

    > [!IMPORTANT]
    > 비밀 URI 끝에 **?api-version=7.0** 을 추가해야 합니다.  

    > [!CAUTION]
    > 출력 보안 설정 옵션을 true로 설정하여 비밀 값이 일반 텍스트로 기록되지 않게 합니다.  이 값을 사용하는 추가 작업의 경우 보안 입력 옵션을 true로 설정해야 합니다.

5. 다른 작업의 값을 사용하려면 코드 식 **@activity('Web1').output.value** 을 사용합니다.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png" alt-text="코드 식":::

## <a name="next-steps"></a>다음 단계

Azure Key Vault를 사용하여 데이터 저장소 및 컴퓨팅을 위한 자격 증명을 저장하는 방법을 알아보려면 [Azure Key Vault에 자격 증명 저장](./store-credentials-in-key-vault.md)을 참조하세요.