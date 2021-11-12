---
title: Azure 부팅 진단
description: Azure 부팅 진단 및 관리형 부팅 진단 개요
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: fdb7b3bcaac2825e64111bdba4ad98554bfc02ae
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339260"
---
# <a name="azure-boot-diagnostics"></a>Azure 부팅 진단

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

부팅 진단은 VM 부팅 실패를 진단할 수 있도록 하는 Azure VM(가상 머신)에 대한 디버깅 기능입니다. 부팅 진단을 통해 사용자는 직렬 로그 정보와 스크린샷을 수집하여 부팅하는 VM의 상태를 관찰할 수 있습니다.

## <a name="boot-diagnostics-storage-account"></a>부팅 진단 스토리지 계정
Azure Portal에서 VM을 만들 때 부팅 진단은 기본적으로 사용하도록 설정됩니다. 권장되는 부팅 진단 환경은 Azure VM을 만들 때 상당한 성능 향상을 제공하므로 관리형 스토리지 계정을 사용하는 것입니다. Azure 관리형 스토리지 계정이 사용되므로 부팅 진단 데이터를 저장할 새 사용자 스토리지 계정을 만드는 데 걸리는 시간이 해소되기 때문입니다.

> [!IMPORTANT]
> 로그와 스냅샷 이미지를 구성하는 부팅 진단 데이터 Blob은 관리형 스토리지 계정에 저장됩니다. 고객은 디스크의 프로비저닝된 크기가 아닌 Blob에서 사용된 GiB에 대해서만 요금이 부과됩니다. 스냅샷 미터는 관리형 스토리지 계정의 요금 청구에 사용됩니다. 관리형 계정은 표준 LRS 또는 표준 ZRS에서 만들어지므로, 고객은 진단 데이터 Blob의 크기에 대해서만 매월 $0.05/GB 요금이 청구됩니다. 이 가격 책정에 대한 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요. 이 요금이 VM 리소스 URI에 연결되는 것을 알 수 있습니다. 

대체 부팅 진단 환경은 사용자 관리형 스토리지 계정을 사용하는 것입니다. 사용자는 새 스토리지 계정을 만들거나 기존 스토리지 계정을 사용할 수 있습니다.
> [!NOTE]
> 부팅 진단과 연결된 사용자 관리 스토리지 계정에는 스토리지 계정이 필요하고 연결된 가상 머신은 동일한 지역 및 구독에 있어야 합니다. 



## <a name="boot-diagnostics-view"></a>부트 진단 보기
가상 머신 블레이드에 있는 부팅 진단 옵션은 Azure Portal의 *지원 및 문제 해결* 섹션 아래에 있습니다. 부팅 진단을 선택하면 스크린샷 및 일련 로그 정보가 표시됩니다. 직렬 로그는 커널 메시징을 포함하고 스크린샷은 VM의 현재 상태에 대한 스냅샷입니다. VM에서 Windows 또는 Linux를 실행하고 있는지 여부에 따라 예상되는 스크린샷이 결과가 결정됩니다. Windows의 경우 사용자에게 바탕 화면 배경이 표시되고, Linux의 경우 로그인 프롬프트가 표시됩니다.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 부팅 진단 스크린샷":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 부팅 진단 스크린샷":::

## <a name="enable-managed-boot-diagnostics"></a>관리형 부팅 진단 사용 
Azure Portal, CLI 및 ARM 템플릿을 통해 관리형 부트 진단을 사용하도록 설정할 수 있습니다. PowerShell을 통해 사용하도록 설정하는 것은 아직 지원되지 않습니다. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Azure Portal을 사용하여 관리형 부팅 진단 사용
Azure Portal에서 VM을 만들 때 기본 설정은 관리형 스토리지 계정을 사용하여 부팅 진단을 사용하도록 설정하는 것입니다. 이 내용을 확인하려면 VM을 만드는 동안 *관리* 탭으로 이동합니다. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="VM을 만드는 동안 관리형 부팅 진단을 사용하도록 설정하는 스크린샷":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>CLI를 사용하여 관리형 부팅 진단 사용
관리형 스토리지 계정을 사용한 부팅 진단은 Azure CLI 2.12.0 이상에서 지원됩니다. 스토리지 계정에 대한 이름 또는 URI를 입력하지 않으면 관리형 계정이 사용됩니다. 자세한 내용 및 코드 샘플은 [부팅 진단에 대한 CLI 설명서](/cli/azure/vm/boot-diagnostics)를 참조하세요.

### <a name="enable-managed-boot-diagnostics-using-powershell"></a>PowerShell을 사용하여 관리되는 부팅 진단 사용
관리되는 스토리지 계정으로 부팅 진단은 Azure PowerShell 6.6.0 이상에서 지원됩니다. 스토리지 계정에 대한 이름 또는 URI를 입력하지 않으면 관리형 계정이 사용됩니다. 자세한 내용 및 코드 샘플은 [부팅 진단에 대한 PowerShell 설명서를 참조하세요.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic?view=azps-6.6.0)

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>ARM(Azure Resource Manager) 템플릿을 사용하여 관리형 부팅 진단 사용
API 버전 2020-06-01 이후의 모든 항목은 관리형 부팅 진단을 지원합니다. 자세한 내용은 [부팅 진단 인스턴스 보기](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics)를 참조하세요.

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>제한 사항
- 관리형 부팅 진단은 Azure Resource Manager VM에만 사용할 수 있습니다. 
- 관리형 부팅 진단은 비관리형 OS 디스크를 사용하는 VM을 지원하지 않습니다.
- 부팅 진단은 Premium Storage 계정 또는 영역 중복 스토리지 계정을 지원하지 않습니다. 이 중 하나가 부팅 진단에 사용되면 VM을 시작할 때 사용자에게 `StorageAccountTypeNotSupported` 오류가 표시됩니다. 
- 관리형 스토리지 계정은 Resource Manager API 버전 "2020-06-01" 이상에서 지원됩니다.
- 현재 Azure 직렬 콘솔은 부팅 진단을 위해 관리형 스토리지 계정과 호환되지 않습니다. [Azure 직렬 콘솔](/troubleshoot/azure/virtual-machines/serial-console-overview)에 대해 자세히 알아보세요.
- 포털은 단일 인스턴스 VM에 대한 관리형 스토리지 계정으로 부팅 진단을 사용하도록 지원합니다.

## <a name="next-steps"></a>다음 단계

[Azure 직렬 콘솔](/troubleshoot/azure/virtual-machines/serial-console-overview)에 대한 자세한 내용과 부팅 진단을 사용하여 [Azure의 가상 머신 문제 해결](/troubleshoot/azure/virtual-machines/boot-diagnostics) 방법을 알아보세요.
