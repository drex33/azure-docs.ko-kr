---
title: 모듈에서 IoT Edge 디바이스 로컬 스토리지 사용 - Azure IoT Edge | Microsoft Docs
description: 환경 변수를 사용하고 IoT Edge 디바이스 로컬 스토리지에 대한 모듈 액세스를 사용 설정하는 옵션을 만듭니다.
author: kgremban
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fc89cbfd01ef827be277d332ecd770b3fa6d7016
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249802"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>모듈에 디바이스의 로컬 스토리지에 대한 액세스 권한 부여

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure 스토리지 서비스를 사용하거나 디바이스의 컨테이너 스토리지에 데이터를 저장하는 것 외에도, 특히 오프라인에서 작동 시 안정성 향상을 위해 호스트 IoT Edge 디바이스 자체에 스토리지를 지정할 수 있습니다.

## <a name="link-module-storage-to-device-storage"></a>디바이스 스토리지에 모듈 스토리지 연결

모듈 스토리지에서 호스트 시스템의 스토리지로 링크를 사용 설정하려면 컨테이너의 스토리지 폴더를 가리키는 모듈에 대해 환경 변수를 만듭니다. 그런 다음, 만들기 옵션을 사용하여 해당 스토리지 폴더를 호스트 머신의 폴더에 바인딩합니다.

예를 들어 IoT Edge 허브가 디바이스의 로컬 스토리지에 메시지를 저장하고 나중에 검색할 수 있도록 하려면 Azure Portal **런타임 설정** 섹션에서 환경 변수와 만들기 옵션을 구성할 수 있습니다.

1. IoT Edge 허브와 IoT Edge 에이전트 모두에서 모듈의 디렉터리를 가리키는 **storageFolder** 라는 환경 변수를 추가합니다.
1. IoT Edge 허브와 IoT Edge 에이전트 모두에 대해 호스트 머신의 로컬 디렉터리를 모듈의 디렉터리에 연결하는 바인딩을 추가합니다. 예를 들면 다음과 같습니다.

   ![로컬 스토리지에 만들기 옵션 및 환경 변수 추가](./media/how-to-access-host-storage-from-module/offline-storage.png)

또는 배포 매니페스트에서 로컬 스토리지를 직접 구성할 수도 있습니다. 예를 들면 다음과 같습니다.

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

`<HostStoragePath>` 및 `<ModuleStoragePath>`를 호스트 및 모듈 스토리지 경로로 바꿉니다. 두 값 모두 절대 경로여야 합니다.

예를 들어 Linux 시스템에서는 `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]`는 컨테이너에서 **/iotedge/storage/** 디렉터리에 매핑된 사용자의 호스트 시스템의 **/etc/iotedge/storage** 디렉터리를 의미합니다. 또 다른 예로 Windows 시스템에서는 `"Binds":["C:\\temp:C:\\contemp"]` 호스트 시스템의 **c: \\ temp** 디렉터리가 컨테이너의 **c: \\**  \ 디렉터리에 매핑되어 있음을 의미합니다.

옵션 만들기에 대한 자세한 내용은 [Docker 문서](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)를 참조하세요.

## <a name="host-system-permissions"></a>호스트 시스템 권한

Linux 장치에서 모듈의 사용자 프로필에 호스트 시스템 디렉터리에 대 한 읽기, 쓰기 및 실행 권한이 있어야 합니다. IoT Edge 허브가 디바이스의 로컬 스토리지에 메시지를 저장하도록 설정하는 이전 예제에서는, UID 1000 사용자 프로필에 권한을 부여해야 합니다. 를 사용 하 여 `chown` 디렉터리 소유자를 변경한 다음 권한을 변경 하는 등 Linux 시스템에서 디렉터리 권한을 관리 하는 방법에는 여러 가지가 있습니다 `chmod` .

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Windows 장치에서 호스트 시스템 디렉터리에 대 한 사용 권한을 구성 해야 합니다. PowerShell을 사용 하 여 사용 권한을 설정할 수 있습니다.

```powershell
$acl = get-acl <HostStoragePath>
$ace = new-object system.security.AccessControl.FileSystemAccessRule('Authenticated Users','FullControl','Allow')
$acl.AddAccessRule($ace)
$acl | Set-Acl
```

## <a name="encrypted-data-in-module-storage"></a>모듈 스토리지의 암호화된 데이터

모듈이 데이터를 암호화하기 위해 IoT Edge 디먼의 워크로드 API를 호출하면 모듈 ID 및 모듈의 생성 ID를 사용하여 암호화 키가 파생됩니다. 생성 ID는 배포에서 모듈이 제거된 다음 나중에 모듈 ID가 동일한 다른 모듈이 동일한 디바이스에 배포되는 경우 비밀을 보호하는 데 사용됩니다. Azure CLI 명령 [az iot hub module-identity show](/cli/azure/iot/hub/module-identity)를 사용하여 모듈의 생성 ID를 확인할 수 있습니다.

여러 세대에 걸친 모듈 간에 파일을 공유하려면 비밀이 포함되지 않아야 합니다. 포함된 경우에는 암호를 해독하지 못합니다.

## <a name="next-steps"></a>다음 단계

모듈에서 호스트 스토리지에 액세스하는 추가적인 예제는 [IoT Edge에서 Azure Blob Storage를 사용하여 에지에 데이터 저장](how-to-store-data-blob.md)을 참조하세요.
