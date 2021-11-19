---
title: Azure PowerShell을 통해 Apache Kafka for Confluent Cloud 만들기 - Azure 파트너 솔루션
description: 이 문서에서는 Azure PowerShell을 사용하여 Apache Kafka for Confluent Cloud 인스턴스를 만드는 방법을 설명합니다.
ms.service: partner-services
ms.topic: quickstart
ms.date: 11/03/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 696e5138d80b1ba799f96fe60da3be0f4f1d2548
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485882"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-powershell"></a>빠른 시작: Apache Kafka for Confluent Cloud 시작 - Azure PowerShell

이 빠른 시작에서는 Azure Marketplace 및 Azure PowerShell을 사용하여 Apache Kafka for Confluent Cloud 인스턴스를 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정. 활성 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure 구독에 대한 _소유자_ 또는 _기여자_ 역할이 있어야 합니다. Azure와 Confluent 간의 통합은 _소유자_ 또는 _기여자_ 액세스 권한이 있는 사용자만 설정할 수 있습니다. 시작하기 전에 [적절한 액세스 권한이 있는지 확인합니다](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>제안 찾기

Azure Portal을 사용하여 Apache Kafka for Confluent Cloud 애플리케이션을 찾습니다.

1. 웹 브라우저에서 [Azure Portal](https://portal.azure.com/)로 이동한 후 로그인합니다.

1. 최근 세션에서 **Marketplace** 를 방문한 경우 사용 가능한 옵션에서 아이콘을 선택합니다. 그렇지 않으면 _Marketplace_ 를 검색합니다.

    :::image type="content" source="media/marketplace.png" alt-text="Marketplace 아이콘":::

1. **Marketplace** 페이지에서 원하는 계획 유형에 따라 두 가지 옵션이 제공됩니다. 종량제 요금제 또는 약정 요금제에 가입할 수 있습니다. 종량제는 공개적으로 사용할 수 있습니다. 약정 요금제는 프라이빗 제안에 대해 승인된 고객이 사용할 수 있습니다.

   - **종량제** 고객의 경우 _Apache Kafka on Confluent Cloud_ 를 검색합니다. Apache Kafka on Confluent Cloud에 대한 제안을 선택합니다.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Azure Marketplace 제안 검색":::

   - **약정** 고객의 경우 **프라이빗 제안 보기** 에 대한 링크를 선택합니다. 약정에 따라 최소 지출 금액에 가입해야 합니다. 이 옵션은 서비스가 오랫동안 필요한 경우에만 사용합니다.

     :::image type="content" source="media/view-private-offers.png" alt-text="프라이빗 제안 보기":::

     _Apache Kafka on Confluent Cloud_ 를 찾습니다.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="프라이빗 제안 선택":::

## <a name="create-resource"></a>리소스 만들기

Apache Kafka on Confluent Cloud에 대한 제안을 선택하면 애플리케이션을 설정할 준비가 된 것입니다.

Azure PowerShell에 대한 환경을 준비하는 것으로 시작합니다.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> **Az.Confluent** PowerShell 모듈이 미리 보기로 제공되는 동안에는 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다.

```azurepowershell
Install-Module -Name Az.Confluent -Scope CurrentUser -Repository PSGallery -Force
```

로그인하면 [New-AzConfluentOrganization](/powershell/module/az.confluent/new-azconfluentorganization) cmdlet을 사용하여 새 조직 리소스를 만듭니다.

```azurepowershell
$ConfluentOrgParams = @{
    Name = 'myOrganization'
    ResourceGroupName = 'myResourceGroup'
    Location = 'my location'
    OfferDetailId = 'string'
    OfferDetailPlanId = 'string'
    OfferDetailPlanName = 'string'
    OfferDetailPublisherId = 'string'
    OfferDetailTermUnit = 'string'
    UserDetailEmailAddress = 'contoso@microsoft.com'
    UserDetailFirstName = 'string'
    UserDetailLastName = 'string'
    Tag = @{Environment='Dev'}
}

New-AzConfluentOrganization @ConfluentOrgParams
```

> [!NOTE]
> 만들기 작업이 완료되기 전에 명령을 반환하려면 선택적 매개 변수 `-NoWait`를 추가합니다. Confluent 조직을 만들 때까지 작업은 계속 실행됩니다.

기존 조직 목록을 보려면 [Get-AzConfluentOrganization](/powershell/module/az.confluent/get-azconfluentorganization) cmdlet을 사용합니다.

구독에 있는 모든 조직을 볼 수 있습니다.

```azurepowershell
Get-AzConfluentOrganization
```

또는 리소스 그룹의 조직을 봅니다.

```azurepowershell
Get-AzConfluentOrganization -ResourceGroupName myResourceGroup
```

특정 조직의 속성을 보려면 `Name` 및 `ResourceGroupName` 매개 변수와 함께 `Get-AzConfluentOrganization` cmdlet을 사용합니다.

다음과 같은 이름을 기준으로 조직을 볼 수 있습니다.

```azurepowershell
Get-AzConfluentOrganization -Name myOrganization -ResourceGroupName myResourceGroup
```

오류가 발생하면 [Apache Kafka for Confluent Cloud 솔루션 문제 해결](troubleshoot.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Confluent Cloud 리소스 관리](manage.md)
