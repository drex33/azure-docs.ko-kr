---
title: Ubuntu Virtual Machines에서 Azure IoT Edge 실행 | Microsoft Docs
description: Ubuntu 18.04 LTS Virtual Machines에 대한 Azure IoT Edge 설정 지침
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: pdecarlo
ms.openlocfilehash: 6865a89eae0df708d73d9062dd07c18c46ece4f8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263765"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ubuntu Virtual Machines에서 Azure IoT Edge 실행

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. 디바이스가 IoT Edge 런타임을 사용하여 구성되면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

IoT Edge 런타임의 작동 방식 및 포함되는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에는 미리 제공된 디바이스 연결 문자열을 사용하여 설치 및 구성된 Azure IoT Edge 런타임으로 Ubuntu 18.04 LTS 가상 머신을 배포하는 단계가 나와 있습니다. 배포는 [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) 프로젝트 리포지토리에서 유지 관리되는 [cloud-init](../virtual-machines/linux/using-cloud-init.md) 기반 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 사용하여 수행됩니다.

처음 부팅할 때 Ubuntu 18.04 LTS 가상 머신은 [cloud-init를 통해 최신 버전의 Azure IoT Edge 런타임을 설치](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt)합니다. 또한 런타임이 시작되기 전에 제공된 연결 문자열을 설정하여 SSH 또는 원격 데스크톱 세션을 시작할 필요 없이 IoT Edge 디바이스를 쉽게 구성하고 연결할 수 있습니다.

## <a name="deploy-using-deploy-to-azure-button"></a>[Azure에 배포] 단추를 사용하여 배포

[Azure에 배포 단추](../azure-resource-manager/templates/deploy-to-azure-button.md)를 사용하면 GitHub에서 유지 관리되는 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 효율적으로 배포할 수 있습니다.  이 섹션에서는 [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) 프로젝트 리포지토리에 포함된 Azure에 배포 단추를 사용하는 방법을 보여줍니다.  

1. iotedge-vm-deploy Azure Resource Manager 템플릿을 사용하여 Azure IoT Edge 지원 Linux VM을 배포합니다.  시작하려면 아래 단추를 클릭합니다.
   :::moniker range="iotedge-2018-06"
   [![iotedge-vm-deploy를 위한 Azure에 배포하기 단추](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)
   :::moniker-end
   :::moniker range="iotedge-2020-11"
   [![iotedge-vm-deploy를 위한 Azure에 배포하기 단추](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2F1.2.0%2FedgeDeploy.json)
   :::moniker-end

1. 새로 시작된 창에서 사용 가능한 양식 필드를 채웁니다.

    > [!div class="mx-imgBorder"]
    > [![iotedge-vm-deploy 템플릿을 보여 주는 스크린샷](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **구독**: 가상 머신을 배포할 활성 Azure 구독입니다.

    **리소스 그룹**: 가상 머신과 연결된 리소스를 포함하는 기존 리소스 그룹 또는 새로 만든 리소스 그룹입니다.

    **DNS 레이블 접두사**: 가상 머신의 호스트 이름을 접두사로 지정하는 데 사용되는 선택 항목의 필수 값입니다.

    **관리자 사용자 이름**: 배포에 대한 루트 권한이 제공되는 사용자 이름입니다.

    **디바이스 연결 문자열**: 의도한 [IoT Hub](../iot-hub/about-iot-hub.md) 내에 만들어진 디바이스에 대한 [디바이스 연결 문자열](./how-to-provision-single-device-linux-symmetric.md#view-registered-devices-and-retrieve-provisioning-information)입니다.

    **VM 크기**: 배포할 가상 머신의 [크기](../cloud-services/cloud-services-sizes-specs.md)입니다.

    **Ubuntu OS 버전**: 기본 가상 머신에 설치할 Ubuntu OS 버전입니다.

    **위치**: 가상 머신을 배포할 [지리적 지역](https://azure.microsoft.com/global-infrastructure/locations/)이며, 이 값은 기본적으로 선택한 리소스 그룹의 위치로 설정됩니다.

    **인증 유형**: 기본 설정에 따라 **sshPublicKey** 또는 **password** 를 선택합니다.

    **관리자 암호 또는 키**: 선택한 인증 유형에 따라 SSH 공개 키 또는 암호의 값입니다.

    모든 필드가 채워지면 페이지 아래쪽의 확인란을 선택하여 약관에 동의하고, **구매** 를 선택하여 배포를 시작합니다.

1. 배포가 성공적으로 완료되었는지 확인합니다.  가상 머신 리소스는 선택한 리소스 그룹에 배포되어야 합니다.  머신 이름을 적어 둡니다. 이는 `vm-0000000000000` 형식이어야 합니다. 또한 연결된 **DNS 이름** 을 기록해 둡니다. 형식은 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com이어야 합니다.

    **DNS 이름** 은 Azure Portal 내에 새로 배포된 가상 머신의 **개요** 섹션에서 가져올 수 있습니다.

    > [!div class="mx-imgBorder"]
    > [![IoT Edge VM의 DNS 이름을 보여 주는 스크린샷](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 설정 후에 SSH를 이 VM에 연결하려면 `ssh <adminUsername>@<DNS_Name>` 명령에서 연결된 **DNS 이름** 을 사용합니다.

## <a name="deploy-from-azure-cli"></a>Azure CLI에서 배포

1. 다음을 사용하여 Azure CLI iot 확장을 설치했는지 확인합니다.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

1. 다음으로, 데스크톱에서 Azure CLI를 사용하는 경우 다음에 로그인하여 시작합니다.

   ```azurecli-interactive
   az login
   ```

1. 구독이 여러 개인 경우 사용할 구독을 선택합니다.
   1. 구독 나열:

      ```azurecli-interactive
      az account list --output table
      ```

   1. 사용하려는 구독의 SubscriptionID 필드를 복사합니다.

   1. 복사한 ID를 사용하여 작업 구독을 설정합니다.

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. 새 리소스 그룹을 만들거나, 다음 단계에서 기존 리소스 그룹을 지정합니다.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 새 가상 머신을 만듭니다.
   :::moniker range="iotedge-2018-06"
   `password`의 **authenticationType** 을 사용하려면 아래 예제를 참조하세요.

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

   SSH 키로 인증하려면 `sshPublicKey`의 **authenticationType** 을 지정한 다음, **adminPasswordOrKey** 매개 변수에서 SSH 키의 값을 제공하면 됩니다.  아래에 예제가 나와 있습니다.

   ```azurecli-interactive
   #Generate the SSH Key
   ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  
    
   #Create a VM using the iotedge-vm-deploy script
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='sshPublicKey' \
   --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
   ```
   :::moniker-end
   :::moniker range="iotedge-2020-11"
   `password`의 **authenticationType** 을 사용하려면 아래 예제를 참조하세요.

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

   SSH 키로 인증하려면 `sshPublicKey`의 **authenticationType** 을 지정한 다음, **adminPasswordOrKey** 매개 변수에서 SSH 키의 값을 제공하면 됩니다.  아래에 예제가 나와 있습니다.

   ```azurecli-interactive
   #Generate the SSH Key
   ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  
    
   #Create a VM using the iotedge-vm-deploy script
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='sshPublicKey' \
   --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
   ```
   :::moniker-end

1. 배포가 성공적으로 완료되었는지 확인합니다.  가상 머신 리소스는 선택한 리소스 그룹에 배포되어야 합니다.  머신 이름을 적어 둡니다. 이는 `vm-0000000000000` 형식이어야 합니다. 또한 연결된 **DNS 이름** 을 기록해 둡니다. 형식은 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com이어야 합니다.

    **DNS 이름** 은 **공용 SSH** 항목의 일부로 **출력** 섹션 내에 있는 이전 단계의 JSON 형식 출력에서 가져올 수 있습니다.  이 항목의 값을 사용하여 새로 배포된 머신에 SSH를 수행할 수 있습니다.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **DNS 이름** 은 Azure Portal 내에 새로 배포된 가상 머신의 **개요** 섹션에서도 가져올 수 있습니다.

    > [!div class="mx-imgBorder"]
    > [![IoT Edge VM의 DNS 이름을 보여 주는 스크린샷](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 설정 후에 SSH를 이 VM에 연결하려면 **명령에서 연결된** DNS 이름`ssh <adminUsername>@<DNS_Name>`을 사용합니다.

## <a name="next-steps"></a>다음 단계

설치된 런타임을 사용하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge 런타임을 제대로 설치하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 체크 아웃하세요.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

SSH 또는 기타 인바운드 연결을 통해 VM에 액세스하는 포트를 열려면 [Linux VM에 대한 포트 및 엔드포인트 열기](../virtual-machines/linux/nsg-quickstart.md)에 대한 Azure Virtual Machines 설명서를 참조하세요.