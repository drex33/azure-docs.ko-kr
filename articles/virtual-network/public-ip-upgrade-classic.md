---
title: 클래식 예약된 IP 주소를 공용 IP 주소로 마이그레이션
titleSuffix: Azure Virtual Network
description: 이 문서에서는 클래식 배포 모델 예약된 IP를 Azure Resource Manager 공용 IP 주소로 업그레이드하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 05355db2d5227db3bf695b059df638e8bc07f4bd
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439181"
---
# <a name="migrate-a-classic-reserved-ip-address-to-a-public-ip-address"></a>클래식 예약된 IP 주소를 공용 IP 주소로 마이그레이션

Azure Resource Manager의 새로운 기능을 활용하려면 클래식 배포 모델에서 Azure Resource Manager로 기존 공용 고정 IP 주소(예약된 IP)를 마이그레이션하면 됩니다.  마이그레이션된 공용 IP는 기본 SKU 형식입니다. 

이 문서에서는 클래식 예약된 IP를 기본 공용 IP 주소로 업그레이드하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* 마이그레이션용으로 등록된 Azure 구독. 자세한 내용은 [PowerShell을 사용하여 Resource Manager로 마이그레이션](../virtual-machines/migration-classic-resource-manager-ps.md)을 참조하세요.
* 클래식 배포 모델 예약된 IP 주소
* PowerShell용으로 설치된 Azure PowerShell 서비스 관리 모듈. 자세한 내용은 [Azure PowerShell 서비스 관리 모듈 설치](/powershell/azure/servicemanagement/install-azure-ps)를 참조하세요.
* Azure CLI 지침용으로 설치된 Azure 클래식 CLI. 자세한 내용은 [Azure 클래식 CLI 설치](/cli/azure/install-classic-cli)를 참조하세요.

## <a name="azure-powershell-service-management-module"></a>Azure PowerShell 서비스 관리 모듈

이 섹션에서는 Azure PowerShell 서비스 관리 모듈을 사용하여 클래식 예약된 IP를 Azure Resource Manager 고정 공용 IP로 마이그레이션합니다.

> [!NOTE]
> IP 주소가 연결된 클라우드 서비스에서 예약된 IP를 제거해야 합니다.

```azurepowershell-interactive
$validate = Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Validate
$validate.ValidationMessages

```

위의 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사에 성공하면 다음 단계를 계속 진행하여 마이그레이션을 **준비** 하고 **커밋** 할 수 있습니다.

```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Prepare
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Commit
```
Azure Resource Manager의 새 리소스 그룹은 마이그레이션된 예약된 IP 이름으로 만들어집니다. 앞의 예제에서 리소스 그룹은 **myReservedIP-Migrated** 입니다.

## <a name="azure-classic-cli"></a>Azure 클래식 CLI

이 섹션에서는 Azure 클래식 CLI를 사용하여 클래식 예약된 IP를 Azure Resource Manager 고정 공용 IP로 마이그레이션합니다.

> [!NOTE]
> IP 주소가 연결된 클라우드 서비스에서 예약된 IP를 제거해야 합니다.

```azurecli-interactive
azure network reserved-ip validate migration myReservedIP

```
위의 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사에 성공하면 다음 단계를 계속 진행하여 마이그레이션을 **준비** 하고 **커밋** 할 수 있습니다.

```azurecli-interactive
azure network reserved-ip prepare-migration myReservedIP
azure network reserved-ip commit-migration myReservedIP
```
Azure Resource Manager의 새 리소스 그룹은 마이그레이션된 예약된 IP 이름으로 만들어집니다. 앞의 예제에서 리소스 그룹은 **myReservedIP-Migrated** 입니다.

## <a name="next-steps"></a>다음 단계


Azure의 공용 IP 주소에 대한 자세한 내용은 다음을 참조하세요.

- [Azure의 공용 IP 주소](public-ip-addresses.md)
- [공용 IP 만들기 - Azure Portal](create-public-ip-portal.md)

