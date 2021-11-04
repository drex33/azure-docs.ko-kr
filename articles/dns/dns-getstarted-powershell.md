---
title: '빠른 시작: Azure DNS 영역 및 레코드 만들기 - Azure PowerShell'
titleSuffix: Azure DNS
description: Azure DNS에 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. Azure PowerShell을 사용하여 첫 번째 DNS 영역 및 레코드를 만들고 관리하는 단계별 빠른 시작입니다.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 04/23/2021
ms.topic: quickstart
ms.service: dns
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: f30fd154f3998166381f4cf3a0341c023480bd5e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080045"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure DNS 영역 및 레코드 만들기

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 빠른 시작에서는 Azure PowerShell을 사용하여 첫 번째 DNS 영역과 레코드를 만듭니다. [Azure Portal](dns-getstarted-portal.md) 또는 [Azure CLI](dns-getstarted-cli.md)를 사용하여 이 단계를 수행할 수도 있습니다. 

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 마지막으로 DNS 영역을 인터넷에 게시하려면 도메인에 대한 이름 서버를 구성해야 합니다. 아래에서는 이러한 각 단계에 대해 설명합니다.

:::image type="content" source="media/dns-getstarted-portal/environment-diagram.png" alt-text="Azure PowerShell을 사용하는 DNS 배포 환경의 다이어그램." border="false":::

Azure DNS는 프라이빗 도메인 만들기도 지원합니다. 첫 번째 프라이빗 DNS 영역 및 레코드를 만드는 방법에 대한 단계별 지침은 [PowerShell을 사용하여 Azure 프라이빗 DNS 영역 시작](private-dns-getstarted-powershell.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

DNS 영역을 만들기 전에 DNS 영역을 포함할 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

DNS 영역은 `New-AzDnsZone` cmdlet을 사용하여 생성됩니다. 다음 예제에서는 *MyResourceGroup* 이라는 리소스 그룹에 *contoso.xyz* 라는 DNS 영역을 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

`New-AzDnsRecordSet` cmdlet을 사용하여 레코드 집합을 만듭니다. 다음 예제에서는 리소스 그룹 "MyResourceGroup"에서 DNS 영역 "contoso.xyz"에 상대적 이름 "www"가 포함된 레코드를 만듭니다. 레코드 집합의 정규화된 이름은 "www.contoso.xyz"입니다. 레코드 형식은 "A"이고, IP 주소는 "10.10.10.10"이며, TTL은 3600초입니다.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 사용하세요.

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>이름 확인 테스트

이제 테스트 'A' 레코드가 포함된 테스트 DNS 영역이 있으므로 *nslookup* 이라는 도구를 사용하여 이름 확인을 테스트할 수 있습니다. 

**DNS 이름 확인을 테스트하려면**

1. 다음 cmdlet을 실행하여 영역에 대한 이름 서버 목록을 가져옵니다.

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. 이전 단계의 출력에서 이름 서버 이름 중 하나를 복사합니다.

1. 명령 프롬프트를 열고 다음 명령을 실행합니다.

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   다음은 그 예입니다.

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   다음 화면과 유사한 출력이 표시됩니다.

   ![스크린샷은 n s 조회 명령과 서버, 주소, 이름 및 주소에 대한 값을 포함하는 명령 프롬프트 창을 보여줍니다.](media/dns-getstarted-portal/nslookup.PNG)

호스트 이름 **www\.contoso.xyz** 는 구성한 대로 **10.10.10.10** 으로 확인됩니다. 이 결과는 이름 확인이 올바르게 작동하는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제하면 이 빠른 시작에서 만든 모든 리소스를 삭제할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure PowerShell을 사용하여 첫 번째 DNS 영역과 레코드를 만들었으면 사용자 지정 도메인에서 웹앱의 레코드를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](./dns-web-sites-custom-domain.md)
