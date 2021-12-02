---
title: Azure CLI를 사용 하 여 SSH 키 만들기
description: Linux Vm에 연결 하기 위한 Azure CLI를 사용 하 여 SSH 키를 생성 하 고 저장 하는 방법을 알아봅니다.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2021
ms.author: cynthn
ms.openlocfilehash: bff2a72efaa872c5354fe3fbeb60b33a4d690223
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440600"
---
# <a name="generate-and-store-ssh-keys-with-the-azure-cli"></a>Azure CLI를 사용 하 여 SSH 키 생성 및 저장

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

VM을 만들기 전에 SSH 키를 만들어 Azure에 저장할 수 있습니다. 새로 만든 각 SSH 키는 로컬에도 저장 됩니다.

기존 SSH 키가 있는 경우 다시 사용 하기 위해 업로드 하 고 Azure에 저장할 수 있습니다.

SSH에 대한 자세한 개요는 [자세한 단계: Azure에서 Linux VM에 인증하기 위한 SSH 키 생성 및 관리](./linux/create-ssh-keys-detailed.md)를 참조하세요.

Linux VM에서 SSH 키를 만들고 사용하는 방법에 대한 자세한 내용은 [SSH 키를 사용하여 Linux VM에 연결](./linux/ssh-from-windows.md)을 참조하세요.

## <a name="generate-new-keys"></a>새 키 생성

Azure CLI에 대한 환경 준비하는 것으로 시작합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. 로그인 한 후 [az sshkey create](/cli/azure/sshkey#az_sshkey_create) 명령을 사용 하 여 새 SSH 키를 만듭니다.

    ```azurecli
    az sshkey create --name "mySSHKey" --resource-group "myResourceGroup"
   ```

1. 결과 출력에는 새 키 파일의 경로가 나열 됩니다.

    ```azurecli
    Private key is saved to "/home/user/.ssh/7777777777_9999999".
    Public key is saved to "/home/user/.ssh/7777777777_9999999.pub".
   ```

1. 개인 키 파일에 대 한 사용 권한을 개인 정보로 변경 합니다.

    ```azurecli
    chmod 600 /home/user/.ssh/7777777777_9999999
    ```

## <a name="connect-to-the-vm"></a>VM에 연결

로컬 컴퓨터에서 Bash 프롬프트를 엽니다.

```azurecli
ssh -identity_file <path to the private key file> username@<ipaddress of the VM>
```

예를 들어 다음과 같이 입력합니다. `ssh -i /home/user/.ssh/mySSHKey azureuser@123.45.67.890`

## <a name="upload-an-ssh-key"></a>SSH 키 업로드

Azure에 저장할 공용 SSH 키를 업로드할 수 있습니다. 

[Az sshkey create](/cli/azure/sshkey#az_sshkey_create) 명령을 사용 하 여 해당 파일을 지정 하 여 SSH 공개 키를 업로드 합니다.

```azurecli
az sshkey create --name "mySSHKey" --public-key "@/home/user/.ssh/7777777777_9999999.pub" --resource-group "myResourceGroup"
```

## <a name="list-keys"></a>키 나열

[Az sshkey list](/cli/azure/sshkey#az_sshkey_list) 명령을 사용 하 여 모든 공용 SSH 키를 나열 하 고 필요에 따라 리소스 그룹을 지정 합니다.

```azurecli
az sshkey list --resource-group "myResourceGroup"
```

## <a name="get-the-public-key"></a>퍼블릭 키 가져오기

[Az sshkey show](/cli/azure/sshkey#az_sshkey_show) 명령을 사용 하 여 공용 SSH 키의 값을 표시 합니다.

```azurecli
az sshkey show --name "mySSHKey" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

Azure VM에서 SSH 키를 사용하는 방법에 대해 자세히 알아보려면 [SSH 키를 사용하여 Linux VM에 연결](./linux/ssh-from-windows.md)을 참조하세요.
