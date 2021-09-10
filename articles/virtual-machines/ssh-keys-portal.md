---
title: Azure Portal에서 SSH 키 만들기
description: Linux VM을 연결하기 위해 Azure Portal에 SSH 키를 생성하고 저장하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: aa6cdb37e6b664398e469ab8e1c3eefbb721bba0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698448"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>Azure Portal에서 SSH 키 생성 및 저장

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Portal을 자주 사용하여 Linux VM을 배포하는 경우 Portal에서 직접 만들거나 컴퓨터에서 업로드하여 SSH 키를 보다 간편하게 사용할 수 있습니다.

먼저 VM을 만들 때 SSH 키를 만들고 다른 VM에 다시 사용할 수 있습니다. 또는 조직 요구에 맞게 키 세트를 Azure에 저장하도록 SSH 키를 별도로 만들 수 있습니다. 

기존 키가 있으며 Portal에서 이 키 사용을 간소화하려면 해당 키를 업로드하고 Azure에 저장하여 다시 사용할 수 있습니다.

Linux VM에서 SSH 키를 만들고 사용하는 방법에 대한 자세한 내용은 [SSH 키를 사용하여 Linux VM에 연결](./linux/ssh-from-windows.md)을 참조하세요.

## <a name="generate-new-keys"></a>새 키 생성

1. [Azure Portal](https://portal.azure.com)을 엽니다.

1. 페이지 맨 위에 *SSH* 를 입력하여 검색합니다. **Marketplace** 에서 **SSH 키** 를 선택합니다.

1. **SSH 키** 페이지에서 **만들기** 를 선택합니다.

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="새 리소스 그룹을 만들고 SSH 키 쌍 생성":::

1. **리소스 그룹** 에서 **새로 만들기** 를 선택하여 키를 저장할 새 리소스 그룹을 만듭니다. 새 리소스 그룹의 이름을 입력하고 **확인** 을 선택합니다.

1. **지역** 에서 키를 저장할 지역을 선택합니다. 이 키를 어떤 지역에서도 사용할 수 있으며 저장되는 지역에 불과합니다.

1. **키 쌍 이름** 에 키의 이름을 입력합니다.

1. **SSH 퍼블릭 키 원본** 에서 **퍼블릭 키 원본 생성** 을 선택합니다. 

1. 완료되면 **검토 + 만들기** 를 선택합니다.

1. 유효성 검사를 통과하면 **만들기** 를 선택합니다.

1. 그런 다음, 팝업 창이 표시되면 **프라이빗 키 다운로드 및 리소스 만들기** 를 선택합니다. 그러면 SSH 키가 .pem 파일로 다운로드됩니다.

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="프라이빗 키를 .pem 파일로 다운로드":::

1. .pem 파일을 다운로드한 후에는 SSH 클라이언트에서 쉽게 가리킬 수 있는 컴퓨터의 위치로 이동할 수 있습니다.


## <a name="connect-to-the-vm"></a>VM에 연결

로컬 컴퓨터에서 PowerShell 프롬프트를 열고 다음을 입력합니다.

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

예를 들어 다음과 같이 입력합니다. `ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>SSH 키 업로드

또한 Azure에 저장할 퍼블릭 SSH 키를 업로드할 수도 있습니다. SSH 키 쌍을 만드는 방법에 대한 자세한 내용은 [ 키를 사용하여 Linux VM에 연결](./linux/ssh-from-windows.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)을 엽니다.

1. 페이지 맨 위에 *SSH* 를 입력하여 검색합니다. **Marketplace* 에서 **SSH 키** 를 선택합니다.

1. **SSH 키** 페이지에서 **만들기** 를 선택합니다.

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="Azure에 저장할 SSH 퍼블릭 키 업로드":::

1. **리소스 그룹** 에서 **새로 만들기** 를 선택하여 키를 저장할 새 리소스 그룹을 만듭니다. 새 리소스 그룹의 이름을 입력하고 **확인** 을 선택합니다.

1. **지역** 에서 키를 저장할 지역을 선택합니다. 이 키를 어떤 지역에서도 사용할 수 있으며 저장되는 지역에 불과합니다.

1. **키 쌍 이름** 에 키의 이름을 입력합니다.

1. **SSH 퍼블릭 키 원본** 에서 **기존 퍼블릭 키 업로드** 를 선택합니다. 

1. 퍼블릭 키의 전체 콘텐츠를 **업로드 키** 에 붙여넣고 **검토 + 만들기** 를 선택합니다.

1. 유효성 검사가 완료되면 **만들기** 를 선택합니다. 

키를 업로드한 후에는 VM을 만들 때 사용하도록 선택할 수 있습니다.

## <a name="list-keys"></a>키 나열

Portal에서 만든 SSH 키는 리소스로 저장되므로 리소스 보기를 필터링하여 모든 항목을 볼 수 있습니다.

1. Portal에서 **모든 리소스** 를 선택합니다.
1. 필터에서 **유형** 을 선택하고 **모두 선택** 옵션을 선택 취소하여 목록을 지웁니다.
1. 필터에 **SSH** 를 입력하고 **SSH 키** 를 선택합니다.

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="모든 SSH 키를 표시하도록 목록을 필터링하는 방법에 대한 스크린샷":::

## <a name="get-the-public-key"></a>퍼블릭 키 가져오기

퍼블릭 키가 필요한 경우 키의 포털 페이지에서 쉽게 복사할 수 있습니다. 키를 나열한 후(마지막 섹션의 프로세스 사용) 목록에서 키를 선택하면 됩니다. 키에 대한 페이지가 열리며, 키 옆의 **클립보드로 복사** 아이콘을 클릭하여 복사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure VM에서 SSH 키를 사용하는 방법에 대해 자세히 알아보려면 [SSH 키를 사용하여 Linux VM에 연결](./linux/ssh-from-windows.md)을 참조하세요.
