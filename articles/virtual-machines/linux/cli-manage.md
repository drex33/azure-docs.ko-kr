---
title: 공통 Azure CLI 명령
description: Azure Resource Manager 모드로 VM 관리를 시작하는 데 사용할 수 있는 일반적인 몇 가지 Azure CLI 명령에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/12/2017
ms.author: cynthn
ms.openlocfilehash: 64f028059504d7d4ff7905653146add0649ecd2f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697006"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Azure 리소스 관리용 일반 Azure CLI 명령

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

Azure CLI를 사용하면 macOS, Linux 및 Windows에서 Azure 리소스를 만들고 관리할 수 있습니다. 이 문서에서는 VM(가상 머신)을 만들고 관리하는 가장 일반적인 몇 가지 명령에 대해 자세히 설명합니다.

이 문서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 브라우저에서 [Cloud Shell](../../cloud-shell/quickstart.md)을 사용할 수도 있습니다.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Azure CLI의 기본 Azure Resource Manager 명령
특정 명령줄 스위치와 옵션에 대해 자세한 도움말은 `az <command> <subcommand> --help`를 입력하여 온라인 명령 도움말과 옵션을 사용할 수 있습니다.

### <a name="create-vms"></a>VM 만들기
| Task | Azure CLI 명령 |
| --- | --- |
| 리소스 그룹 만들기 | `az group create --name myResourceGroup --location eastus` |
| Linux VM 만들기 | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Windows VM 만들기 | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>VM 상태 관리
| Task | Azure CLI 명령 |
| --- | --- |
| VM 시작 | `az vm start --resource-group myResourceGroup --name myVM` |
| VM 중지 | `az vm stop --resource-group myResourceGroup --name myVM` |
| VM 할당 취소 | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| VM 다시 시작 | `az vm restart --resource-group myResourceGroup --name myVM` |
| VM 재배포 | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| VM 삭제 | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>VM 정보 가져오기
| Task | Azure CLI 명령 |
| --- | --- |
| VM 나열 | `az vm list` |
| VM에 대한 정보 가져오기 | `az vm show --resource-group myResourceGroup --name myVM` |
| VM 리소스 사용 | `az vm list-usage --location eastus` |
| 모든 사용 가능한 VM 크기 가져오기 | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>디스크 및 이미지
| Task | Azure CLI 명령 |
| --- | --- |
| VM에 데이터 디스크 추가 | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| VM에서 데이터 디스크 제거 | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| 디스크 크기 조정 | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| 디스크 스냅샷 | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| VM 이미지 만들기 | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| 이미지에서 VM 만들기 | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>다음 단계
CLI 명령의 추가 예제는 [Azure CLI를 사용하여 Linux VM 만들기 및 관리](tutorial-manage-vm.md) 자습서를 참조하세요.
