---
title: Linux에 SMB Azure 파일 공유 탑재 | Microsoft Docs
description: Linux에서 SMB를 통해 Azure 파일 공유를 탑재하는 방법을 알아봅니다. 사전 요구 사항 목록을 참조하세요. Linux 클라이언트에서 SMB 보안 고려 사항을 검토하세요.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 417955b723ef5b5d70583c7b101eb39fe29fa694
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179205"
---
# <a name="mount-smb-azure-file-share-on-linux"></a>Linux에 SMB Azure 파일 공유 탑재
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 [SMB 커널 클라이언트](https://wiki.samba.org/index.php/LinuxCIFS)를 사용하여 Linux 배포판에 탑재할 수 있습니다.

Linux에서 Azure 파일 공유를 탑재하는 권장 방법은 SMB 3.1.1을 사용하는 것입니다. 기본적으로 Azure Files는 전송 중에 암호화가 필요하며 이는 SMB 3.0 이상에서 지원됩니다. Azure Files는 전송 중에 암호화를 지원하지 않는 SMB 2.1도 지원합니다. 하지만 보안상의 이유로 다른 Azure 지역 또는 온-프레미스에서 SMB 2.1을 사용하여 Azure 파일 공유를 탑재하지 않을 수 있습니다. 애플리케이션에서 SMB 2.1을 특별히 요구하지 않는 한 SMB 3.1.1을 사용합니다.

| 배포 | SMB 3.1.1 | SMB 3.0 |
|-|-----------|---------|
| Linux 커널 버전 | <ul><li>기본 3.1.1 지원: 4.17</li><li>기본 탑재: 5.0</li><li>AES-128-GCM 암호화: 5.3</li></ul> | <ul><li>기본 3.0 지원: 3.12</li><li>AES-128-CCM 암호화: 4.11</li></ul> |
| [Ubuntu](https://wiki.ubuntu.com/Releases) | AES-128-GCM 암호화: 18.04.5 LTS 이상 | AES-128-CCM 암호화: 16.04.4 LTS 이상 |
| [Red Hat Enterprise Linux(RHEL)](https://access.redhat.com/articles/3078) | <ul><li>기본: 8.0+</li><li>기본 탑재: 8.2 이상</li><li>AES-128-GCM 암호화: 8.2 이상</li></ul> | 7.5+ |
| [Debian](https://www.debian.org/releases/) | 기본: 10+ | AES-128-CCM 암호화: 10 이상 |
| [SUSE Linux Enterprise Server](https://www.suse.com/support/kb/doc/?id=000019587) | AES-128-GCM 암호화: 15 SP2 이상 | AES-128-CCM 암호화: 12 SP2 이상 |

Linux 배포가 위 표에 나열되지 않은 경우 `uname` 명령을 사용하여 Linux 커널 버전을 확인할 수 있습니다.

```bash
uname -r
```

> [!Note]  
> SMB 2.1 지원이 Linux 커널 버전 3.7에 추가되었습니다. 3\.7 이후 버전의 Linux 커널을 사용하는 경우 SMB 2.1을 지원해야 합니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>필수 구성 요소
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**cifs-utils 패키지가 설치되어 잇는 확인합니다.**  
    cifs-utils는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian** 에서는 `apt` 패키지 관리자를 사용합니다.

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    **Red Hat Enterprise Linux 8 이상** 에서는 `dnf` 패키지 관리자를 사용합니다.

    ```bash
    sudo dnf install cifs-utils
    ```

    **Red Hat Enterprise Linux** 의 이전 버전에서는 `yum` 패키지 관리자를 사용합니다.

    ```bash
    sudo yum install cifs-utils 
    ```

    **SUSE Linux Enterprise Server** 에서 `zypper` 패키지 관리자를 사용합니다.

    ```bash
    sudo zypper install cifs-utils
    ```

    다른 배포판에서는 적절한 패키지 관리자를 사용하거나 [소스에서 컴파일합니다](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **최신 버전의 Azure CLI(Azure 명령줄 인터페이스)를 사용해야 합니다.** Azure CLI를 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell CLI 설치](/cli/azure/install-azure-cli)를 참조하고 해당 운영 체제를 선택합니다. PowerShell 6 이상에서 Azure PowerShell 모듈을 사용하려는 경우 이 문서의 지침은 Azure CLI에 대한 것입니다.

* **445 포트가 열려 있는지 확인합니다**. SMB는 445 TCP 포트를 통해 통신합니다. 방화벽이 클라이언트 컴퓨터에서 445 TCP 포트를 차단하고 있지 않은지 확인합니다.  `<your-resource-group>` 및 `<your-storage-account>`를 바꾸고 다음 스크립트를 실행합니다.

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" --output tsv | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    연결되면 다음 출력과 유사한 내용이 표시됩니다.

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    회사 네트워크에서 포트 445를 열 수 없거나 ISP에 의해 이러한 작업이 차단된 경우 VPN 연결 또는 ExpressRoute를 사용하여 포트 445를 처리할 수 있습니다. 자세한 내용은 [직접 Azure 파일 공유 액세스를 위한 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조하세요.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>요청 시 탑재를 사용하여 Azure 파일 공유 탑재
Linux OS에 파일 공유를 탑재하면 원격 파일 공유가 로컬 파일 시스템의 폴더로 표시됩니다. 파일 공유를 시스템의 어디에나 탑재할 수 있습니다. 다음 예는 `/mount` 경로 아래에 탑재됩니다. `$mntRoot` 변수를 수정하여 원하는 경로로 변경할 수 있습니다.

`<resource-group-name>`, `<storage-account-name>` 및 `<file-share-name>`을 사용자 환경에 적합한 정보로 바꾸어야 합니다.

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"
fileShareName="<file-share-name>"

mntRoot="/mount"
mntPath="$mntRoot/$storageAccountName/$fileShareName"

sudo mkdir -p $mntPath
```

다음으로 `mount` 명령을 사용하여 파일 공유를 탑재합니다. 다음 예에서 `$smbPath` 명령은 스토리지 계정의 파일 엔드포인트에 대한 완전히 정규화된 도메인 이름을 사용하여 채워지고 `$storageAccountKey`는 스토리지 계정 키로 채워집니다. 

# <a name="smb-311"></a>[SMB 3.1.1](#tab/smb311)
> [!Note]  
> Linux 커널 버전 5.0부터 SMB 3.1.1이 기본 협상 프로토콜입니다. 5\.0 이전의 Linux 커널 버전을 사용하는 경우 탑재 옵션 목록에서 `vers=3.1.1`을 지정합니다.  

```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" --output tsv | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-30"></a>[SMB 3.0](#tab/smb30)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" --output tsv | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-21"></a>[SMB 2.1](#tab/smb21)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" --output tsv | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=2.1,username=$storageAccountName,password=$storageAccountKey,serverino
```

---

`mount` 명령의 탑재 옵션에서 `uid`/`gid` 또는 `dir_mode` 및 `file_mode`를 사용하여 권한을 설정할 수 있습니다. 권한 설정 방법에 대한 자세한 내용은 위키백과에서 [UNIX 숫자 표기법](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)을 참조하세요.

원하는 경우 동일한 Azure 파일 공유를 여러 탑재 지점에 탑재할 수도 있습니다. Azure 파일 공유 사용을 마치면 `sudo umount $mntPath`를 사용하여 공유를 탑재 해제합니다.

## <a name="automatically-mount-file-shares"></a>파일 공유 자동 탑재
Linux OS에 파일 공유를 탑재하면 원격 파일 공유가 로컬 파일 시스템의 폴더로 표시됩니다. 파일 공유를 시스템의 어디에나 탑재할 수 있습니다. 다음 예는 `/mount` 경로 아래에 탑재됩니다. `$mntRoot` 변수를 수정하여 원하는 경로로 변경할 수 있습니다.

```bash
mntRoot="/mount"
sudo mkdir -p $mntRoot
```

Linux에서 Azure 파일 공유를 탑재하려면 스토리지 계정 이름을 파일 공유의 사용자 이름으로 사용하고 스토리지 계정 키를 암호로 사용합니다. 스토리지 계정 자격 증명은 시간이 지남에 따라 변경될 수 있기 때문에 탑재 구성과 별도로 스토리지 계정에 대한 자격 증명을 저장해야 합니다. 

다음 예에서는 자격 증명을 저장할 파일을 만드는 방법을 보여 줍니다. `<resource-group-name>` 및 `<storage-account-name>`를 사용자 환경에 대한 적절한 정보로 바꾸어야 합니다.

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"

# Create a folder to store the credentials for this storage account and
# any other that you might set up.
credentialRoot="/etc/smbcredentials"
sudo mkdir -p "/etc/smbcredentials"

# Get the storage account key for the indicated storage account.
# You must be logged in with az login and your user identity must have 
# permissions to list the storage account keys for this command to work.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" --output tsv | tr -d '"')

# Create the credential file for this individual storage account
smbCredentialFile="$credentialRoot/$storageAccountName.cred"
if [ ! -f $smbCredentialFile ]; then
    echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
    echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
else 
    echo "The credential file $smbCredentialFile already exists, and was not modified."
fi

# Change permissions on the credential file so only root can read or modify the password file.
sudo chmod 600 $smbCredentialFile
```

파일 공유를 자동으로 탑재하려면 `/etc/fstab` 유틸리티를 통한 정적 탑재 사용 또는 `autofs` 유틸리티를 통한 동적 탑재 사용 중에서 선택할 수 있습니다. 

### <a name="static-mount-with-etcfstab"></a>/etc/fstab을 사용하여 정적 탑재
이전 환경을 사용하여 탑재 폴더 아래에 스토리지 계정/파일 공유에 대한 폴더를 만듭니다. `<file-share-name>`을 Azure 파일 공유의 적절한 이름으로 바꿉니다.

```bash
fileShareName="<file-share-name>"

mntPath="$mntRoot/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath
```

마지막으로 Azure 파일 공유에 대한 `/etc/fstab` 파일에 레코드를 만듭니다. 아래 명령에서 기본 0755 Linux 파일 및 폴더 권한은 소유자에 대한 읽기, 쓰기 및 실행(파일/디렉터리 Linux 소유자 기반), 소유자 그룹의 사용자에 대한 읽기 및 실행, 시스템의 다른 사용자에 대한 읽기 및 실행을 의미합니다. 원하는 대로 탑재 시 대체 `uid` 및 `gid` 또는 `dir_mode` 및 `file_mode` 권한을 설정할 수 있습니다. 권한 설정 방법에 대한 자세한 내용은 위키백과에서 [UNIX 숫자 표기법](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)을 참조하세요.

```bash
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
    echo "$smbPath $mntPath cifs nofail,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
else
    echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
fi

sudo mount -a
```

> [!Note]  
> Linux 커널 버전 5.0부터 SMB 3.1.1이 기본 협상 프로토콜입니다. `vers` 탑재 옵션을 사용하여 대체 프로토콜 버전을 지정할 수 있습니다(프로토콜 버전은 `3.1.1`, `3.0` 및 `2.1`임).

### <a name="dynamically-mount-with-autofs"></a>autofs를 사용하여 동적 탑재
`autofs` 유틸리티를 사용하여 파일 공유를 동적으로 탑재하려면 선택한 Linux 배포에 패키지 관리자를 사용하여 설치합니다.  

**Ubuntu** 및 **Debian** 배포판에서는 `apt` 패키지 관리자를 사용합니다.

```bash
sudo apt update
sudo apt install autofs
```

**Red Hat Enterprise Linux 8 이상** 에서는 `dnf` 패키지 관리자를 사용합니다.
```bash
sudo dnf install autofs
```

**Red Hat Enterprise Linux** 의 이전 버전에서는 `yum` 패키지 관리자를 사용합니다.

```bash
sudo yum install autofs 
```

**SUSE Linux Enterprise Server** 에서 `zypper` 패키지 관리자를 사용합니다.
```bash
sudo zypper install autofs
```

다음으로 `autofs` 구성 파일을 업데이트합니다. 

```bash
fileShareName="<file-share-name>"

httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" --output tsv | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath" > /etc/auto.fileshares

echo "/fileshares /etc/auto.fileshares --timeout=60" > /etc/auto.master
```

마지막 단계는 `autofs` 서비스를 다시 시작하는 것입니다.

```bash
sudo systemctl restart autofs
```

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [Linux에서 SMB 1 제거](files-remove-smb1-linux.md)
- [문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
