---
title: '자습서: Azure IoT Edge 디바이스 구성 - IoT Edge의 Machine Learning'
description: 이 자습서에서는 Linux를 실행하는 Azure 가상 머신을 투명 게이트웨이로 작동하는 Azure IoT Edge 디바이스로 구성합니다.
author: kgremban
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 103a3004f6bf4ec6e536d87b7fe72484f2bff974
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259115"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>자습서: Azure IoT Edge 디바이스 구성

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 Linux를 실행하는 Azure 가상 머신을 투명 게이트웨이로 작동하는 Azure IoT Edge 디바이스로 구성합니다. 투명 게이트웨이를 구성하면 디바이스가 게이트웨이 존재 여부를 알지 못해도 게이트웨이를 통해 Azure IoT Hub에 연결할 수 있습니다. 그와 동시에, IoT Hub의 디바이스와 상호 작용하는 사용자는 중간에 있는 게이트웨이 디바이스를 인식하지 못합니다. 궁극적으로 IoT Edge 모듈을 투명 게이트웨이에 추가하여 에지 분석 기능을 시스템에 추가합니다.

>[!NOTE]
>이 자습서의 개념은 모든 버전의 IoT Edge에 적용되지만 시나리오를 사용해보기 위해 만드는 샘플 디바이스는 IoT Edge 버전 1.1을 실행합니다.

이 문서의 단계는 일반적으로 클라우드 개발자가 수행합니다.

자습서의 이 섹션에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 게이트웨이 디바이스가 다운스트림 디바이스에 안전하게 연결할 수 있도록 인증서를 만듭니다.
> * IoT Edge 디바이스를 만듭니다.
> * IoT Edge 디바이스를 시뮬레이션하는 Azure 가상 머신을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서는 IoT Edge에서 Azure Machine Learning을 사용하는 방법에 대한 자습서 시리즈의 일부입니다. 시리즈의 각 문서는 이전 문서의 작업을 기반으로 합니다. 시리즈의 다른 문서를 참조하지 않고 이 문서를 직접 참조한 경우 시리즈의 [첫 번째 문서](tutorial-machine-learning-edge-01-intro.md)를 참조하세요.

## <a name="create-certificates"></a>인증서 만들기

디바이스가 게이트웨이로 작동하려면 다운스트림 디바이스에 안전하게 연결해야 합니다. IoT Edge를 사용하면 PKI(공개 키 인프라)를 사용하여 디바이스 간의 보안 연결을 설정할 수 있습니다. 이 경우에 투명 게이트웨이로 작동하는 IoT Edge 디바이스에 다운스트림 IoT 디바이스를 연결할 수 있습니다. 적절한 보안 유지를 위해 다운스트림 디바이스는 IoT Edge 디바이스의 ID를 확인해야 합니다. IoT Edge 디바이스에서 인증서를 사용하는 방법에 대한 자세한 내용은 [Azure IoT Edge 인증서 사용 현황 세부 정보](iot-edge-certs.md)를 참조하세요.

이 섹션에서는 Docker 이미지를 사용하여 자체 서명된 인증서를 만든 다음, 이를 빌드하고 실행합니다. Windows 개발 컴퓨터에서 인증서를 만드는 데 필요한 단계 수가 줄어들므로 Docker 이미지를 사용하여 이 단계를 완료하기로 선택했습니다. Docker 이미지를 사용하여 자동화한 기능을 이해하려면 [데모 인증서를 만들어 IoT Edge 디바이스 기능 테스트](how-to-create-test-certificates.md)를 참조하세요.

1. 개발 VM에 로그인합니다.
1. 경로와 이름이 **c:\edgeCertificates** 인 새 폴더를 만듭니다.

1. 아직 실행하지 않은 경우 Windows 시작 메뉴에서 **Windows용 Docker** 를 시작합니다.

1. Visual Studio Code를 엽니다.

1. **파일** > **폴더 열기** 를 차례로 선택한 다음, **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates** 를 선택합니다.

1. **탐색기** 창에서 마우스 오른쪽 단추로 **dockerfile** 을 클릭하고, **이미지 빌드** 를 선택합니다.

1. 대화 상자에서 이미지 이름 및 태그에 대한 기본값인 **createcertificates: latest** 를 적용합니다.

    ![Visual Studio Code에서 인증서 만들기를 보여 주는 스크린샷](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. 빌드가 완료될 때까지 기다립니다.

    > [!NOTE]
    > 공개 키가 없다는 경고가 표시될 수 있습니다. 이 경고를 무시해도 안전합니다. 마찬가지로 이미지에 대한 권한을 확인하거나 다시 설정하도록 권장하는 보안 경고가 표시되며, 이 이미지에서 무시해도 안전합니다.

1. Visual Studio Code 터미널 창에서 createcertificates 컨테이너를 실행합니다.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker가 **c:\\** 드라이브에 대한 액세스를 요청합니다. **공유** 를 선택합니다.

1. 자격 증명을 입력하라는 메시지가 표시되면 입력합니다.

1. 컨테이너 실행이 완료되면 **c:\\edgeCertificates** 에서 다음 파일이 있는지 확인합니다.

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Azure Key Vault에 인증서 업로드

인증서를 안전하게 저장하고 여러 디바이스에서 액세스할 수 있도록 인증서를 Azure Key Vault에 업로드합니다. 위의 목록에서 볼 수 있듯이 두 가지 형식(PFX 및 PEM)의 인증서 파일이 있습니다. PFX 파일은 Key Vault에 업로드할 Key Vault 인증서로 간주됩니다. PEM 파일은 일반 텍스트이며 Key Vault 비밀로 간주됩니다. [Jupyter Notebook](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks)을 실행하여 만든 Azure Machine Learning 작업 영역과 연결된 Key Vault를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning 작업 영역으로 이동합니다.

1. Machine Learning 작업 영역의 개요 페이지에서 **Key Vault** 이름을 찾습니다.

    ![Key Vault 이름의 복사를 보여 주는 스크린샷](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. 개발 머신에서 Key Vault에 인증서를 업로드합니다. **\<subscriptionId\>** 및 **\<keyvaultname\>** 을 리소스 정보로 바꿉니다.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. 메시지가 표시되면 Azure에 로그인합니다.

1. 스크립트가 새 Key Vault 항목을 나열하는 출력과 함께 몇 분 동안 실행됩니다.

    ![Key Vault 스크립트 출력을 보여 주는 스크린샷](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="register-an-iot-edge-device"></a>IoT Edge 디바이스 등록

Azure IoT Edge 디바이스를 IoT 허브에 연결하려면 먼저 디바이스를 허브에 등록합니다. 클라우드의 디바이스 ID에서 연결 문자열을 가져와 IoT Edge 디바이스의 런타임을 구성하는 데 사용합니다. 구성된 디바이스가 허브에 연결되면 모듈을 배포하고 메시지를 보낼 수 있습니다. IoT Hub에서 해당 디바이스 ID를 변경하여 물리적 IoT Edge 디바이스의 구성을 변경할 수도 있습니다.

이 자습서에서는 Visual Studio Code를 사용하여 새 디바이스 ID를 등록합니다. 이러한 단계는 Azure Portal 또는 Azure CLI를 사용하여 완료할 수도 있습니다. 어떤 방법을 선택하든 IoT Edge 디바이스의 디바이스 연결 문자열을 가져와야 합니다. 디바이스 연결 문자열은 Azure Portal의 디바이스 세부 정보 페이지에서 확인할 수 있습니다.

1. 개발 머신에서 Visual Studio Code를 엽니다.

1. Visual Studio Code **탐색기** 보기에서 **Azure IoT Hub** 프레임을 펼칩니다.

1. 줄임표를 선택하고, **IoT Edge 디바이스 만들기** 를 선택합니다.

1. 디바이스 이름을 지정합니다. 편의상 **aaTurbofanEdgeDevice** 라는 이름을 사용하여 나열된 디바이스의 위쪽에 정렬합니다.

1. 디바이스 목록에 새 디바이스가 표시됩니다.

    ![Visual Studio Code 탐색기의 디바이스 보기를 보여 주는 스크린샷](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Azure 가상 머신 배포

Azure IoT Edge 런타임이 설치되고 구성된 Ubuntu 18.04 LTS 가상 머신을 사용합니다. 배포에서는 [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) 프로젝트 리포지토리에서 유지 관리되는 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 사용합니다. 템플릿에 제공하는 연결 문자열을 사용하여 이전 단계에서 등록한 IoT Edge 디바이스를 프로비전합니다.

Azure Portal 또는 Azure CLI를 사용하여 가상 머신을 배포할 수 있습니다. Azure Portal 단계가 표시됩니다. 자세한 내용은 [Ubuntu Virtual Machines에서 Azure IoT Edge 실행](how-to-install-iot-edge-ubuntuvm.md)을 참조하세요.

### <a name="deploy-using-deploy-to-azure-button"></a>[Azure에 배포] 단추를 사용하여 배포

1. `iotedge-vm-deploy` ARM 템플릿을 사용하여 Ubuntu 18.04 LTS 가상 머신을 배포하려면 아래 단추를 클릭합니다.

    [![iotedge-vm-deploy를 위한 Azure에 배포하기 단추](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 새로 시작된 창에서 사용 가능한 양식 필드를 채웁니다.

   | 필드 | 설명 |
   | - | - |
   | **구독** | 가상 머신을 배포할 활성 Azure 구독입니다. |
   | **리소스 그룹** | 가상 머신 및 연결되는 해당 리소스를 포함할 기존 리소스 그룹 또는 새로 만든 리소스 그룹입니다. |
   | **DNS 레이블 접두사** | 가상 머신의 호스트 이름을 접두사로 지정하는 데 사용되는 선택 항목의 필수 값입니다. |
   | **관리자 사용자 이름** | 배포에 대한 루트 권한이 제공되는 사용자 이름입니다. |
   | **디바이스 연결 문자열** | 의도한 [IoT Hub](../iot-hub/about-iot-hub.md) 내에서 만들어진 디바이스에 대한 [디바이스 연결 문자열](./how-to-provision-single-device-linux-symmetric.md#view-registered-devices-and-retrieve-provisioning-information)입니다. |
   | **VM 크기** | 배포할 가상 머신의 [크기](../cloud-services/cloud-services-sizes-specs.md)입니다.
   | **Ubuntu OS 버전** | 기본 가상 머신에 설치할 Ubuntu OS의 버전입니다. |
   | **위치** | 가상 머신을 배포할 [지리적 지역](https://azure.microsoft.com/global-infrastructure/locations/)입니다. 이 값은 기본적으로 선택한 리소스 그룹의 위치로 설정됩니다. |
   | **인증 유형** | 기본 설정에 따라 **sshPublicKey** 또는 **password** 를 선택합니다. |
   | **관리자 암호 또는 키** | 선택한 인증 유형에 따른 SSH 공개 키 또는 암호의 값입니다. |

1. 모든 필드가 채워지면 페이지 아래쪽의 확인란을 선택하여 약관에 동의하고, **검토 + 만들기** 및 **만들기** 를 선택하여 배포를 시작합니다.

1. Azure Portal에서 가상 머신으로 이동합니다. 리소스 그룹을 통하거나 포털 방문 페이지의 **Azure 서비스** 아래에서 **가상 머신** 을 선택하여 찾을 수 있습니다.

1. 가상 머신의 **DNS 이름** 을 적어 둡니다. 이는 가상 머신에 로그온하는 데 필요합니다.

## <a name="connect-to-your-iot-edge-device"></a>IoT Edge 디바이스에 연결

1. 명령 프롬프트를 열고, 다음 명령을 사용하여 가상 머신에 로그온합니다. 이전 섹션에 따라 사용자 이름 및 DNS 이름에 대한 사용자 고유의 정보를 입력합니다.

    ```bash
    ssh <adminUsername>@<DNS_name>
    ```

1. 호스트의 신뢰성에 대한 유효성을 검사하라는 메시지가 표시되면 **yes** 를 입력하고 **Enter** 키를 선택합니다.

1. 메시지가 표시되면 암호를 제공합니다.

1. Ubuntu에서 환영 메시지를 표시한 다음, `<username>@<machinename>:~$`와 같은 프롬프트가 표시됩니다.

## <a name="download-key-vault-certificates"></a>Key Vault 인증서 다운로드

이 문서의 앞부분에서 IoT Edge 디바이스 및 리프 디바이스에 사용할 수 있도록 Key Vault에 인증서를 업로드했습니다. 리프 디바이스는 IoT Edge 디바이스를 게이트웨이로 사용하여 IoT Hub와 통신하는 다운스트림 디바이스입니다.

리프 디바이스는 자습서의 뒷부분에서 설명합니다. 이 섹션에서는 IoT Edge 디바이스에 인증서를 다운로드하겠습니다.

1. Linux 가상 머신의 SSH 세션에서 Azure CLI를 사용하여 Azure에 로그인합니다.

    ```azurecli
    az login
    ```

1. [Microsoft 디바이스 로그인](https://microsoft.com/devicelogin) 페이지에 대한 브라우저를 열고, 고유한 코드를 제공하라는 메시지가 표시됩니다. 이 단계를 로컬 머신에서 수행할 수도 있습니다. 인증을 마쳤으면 브라우저 창을 닫습니다.

1. 성공적으로 인증하면 Linux VM이 로그인되어 Azure 구독을 나열합니다.

1. Azure CLI 명령에 사용할 Azure 구독을 설정합니다.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. VM에 인증서 디렉터리를 만듭니다.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. 키 자격 증명 모음에 저장한 인증서(new-edge-device-full-chain.cert.pem, new-edge-device.key.pem 및 azure-iot-test-only.root.ca.cert.pem)를 다운로드합니다.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>IoT Edge 디바이스 구성 업데이트

IoT Edge 런타임은 /etc/iotedge/config.yaml 파일을 사용하여 구성을 유지합니다. 이 파일에서 두 가지 정보를 업데이트해야 합니다.

* **인증서**: 다운스트림 디바이스와 연결하는 데 사용할 인증서
* **호스트 이름**: VM IoT Edge 디바이스의 FQDN(정규화된 도메인 이름)

config.yaml 파일을 직접 편집하여 certificates 및 hostname을 업데이트합니다.

1. config.yaml 파일을 엽니다.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. 선행 **#** 을 제거하고 파일이 다음 예제와 같이 보이도록 경로를 설정하여 config.yaml의 certificates 섹션을 업데이트합니다.

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    **certificates:** 줄에 선행 공백이 없고 중첩된 각 인증서가 두 개의 공백으로 들여쓰였는지 확인합니다.

    nano를 마우스 오른쪽 단추로 클릭하면 클립보드의 내용이 현재 커서 위치에 붙여넣기 됩니다. 문자열을 바꾸려면 키보드 화살표를 사용하여 바꾸려는 문자열로 이동하고, 해당 문자열을 삭제한 다음, 마우스 오른쪽 단추를 클릭하여 버퍼에서 붙여넣습니다.

1. Azure Portal에서 가상 머신으로 이동합니다. **개요** 섹션에서 DNS 이름(머신의 FQDN)을 복사합니다.

1. FQDN을 config.yml 파일의 hostname 섹션에 붙여넣습니다. 이름이 모두 소문자인지 확인합니다.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. **Ctrl+X**, **Y** 및 **Enter** 키를 선택하여 파일을 저장하고 닫습니다.

1. IoT Edge 디먼을 다시 시작합니다.

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge 디먼의 상태를 확인합니다. 명령 뒤에 **:q** 를 입력하여 종료합니다.

    ```bash
    systemctl status iotedge
    ```

## <a name="troubleshooting"></a>문제 해결

상태에 오류("\[ERROR\]"라는 접두사가 붙은 색이 지정된 텍스트)가 표시되면 디먼 로그에서 자세한 오류 정보를 검사합니다.

   ```bash
   journalctl -u iotedge --no-pager --no-full
   ```

오류를 해결하는 방법에 대한 자세한 내용은 [문제 해결](troubleshoot.md) 페이지를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서는 각 문서가 이전 작업에서 수행된 작업을 기반으로 하는 집합의 일부입니다. 최종 자습서가 완료될 때까지 기다렸다가 리소스를 정리합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VM을 Azure IoT Edge 투명 게이트웨이로 구성했습니다. 먼저 테스트 인증서를 생성하여 Key Vault에 업로드했습니다. 다음으로 스크립트 및 Resource Manager 템플릿을 사용하여 VM을 Azure Marketplace의 Ubuntu Server 16.04 LTS + Azure IoT Edge 런타임 이미지로 배포했습니다. VM을 가동하여 실행하고, SSH를 통해 연결했습니다. 그런 다음, Azure에 로그인하고 Key Vault에서 인증서를 다운로드했습니다. config.yaml 파일을 업데이트하여 IoT Edge 런타임의 몇 가지 구성을 업데이트했습니다.

다음 문서를 계속 진행하여 IoT Edge 모듈을 빌드하세요.

> [!div class="nextstepaction"]
> [사용자 지정 IoT Edge 모듈 만들기 및 배포](tutorial-machine-learning-edge-06-custom-modules.md)
