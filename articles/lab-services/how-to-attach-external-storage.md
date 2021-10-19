---
title: Azure Lab Services에서 외부 파일 스토리지 사용 | Microsoft Docs
description: Lab Services에서 외부 파일 스토리지를 사용하는 랩을 설정하는 방법에 대해 알아봅니다.
author: emaher
ms.topic: how-to
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: f7c92e4369491a5b371c2f99fbcbdc4609b376f7
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180973"
---
# <a name="use-external-file-storage-in-lab-services"></a>Lab Services에서 외부 파일 스토리지 사용

이 문서에서는 Azure Lab Services를 사용할 때 외부 파일 스토리지에 대한 몇 가지 옵션을 설명합니다. [Azure Files](https://azure.microsoft.com/services/storage/files/)는 [SMB 2.1 및 SMB 3.0을 통해 액세스할 수 있는](../storage/files/storage-how-to-use-files-windows.md) 클라우드에서 완전 관리형 파일 공유를 제공합니다. Azure Files 공유는 가상 네트워크에서 퍼블릭으로 또는 프라이빗으로 연결할 수 있습니다. 파일 공유에 연결하기 위해 학생 Active Directory 자격 증명을 사용하도록 공유를 구성할 수도 있습니다. Linux 컴퓨터를 사용하는 경우 Azure Lab Services에서 외부 파일 스토리지용 NFS 볼륨과 함께 Azure NetApp Files를 사용할 수도 있습니다.  

## <a name="which-solution-to-use"></a>사용할 솔루션

각 솔루션에는 다양한 요구 사항 및 기능이 있습니다. 다음 표에는 각 솔루션에 대해 고려할 중요 사항이 정리되어 있습니다.  

|     솔루션    |    알아야 할 중요 사항    |
| -------------- | ------------------------ |
| [퍼블릭 엔드포인트를 사용하는 Azure Files 공유](#azure-files-share) | <ul><li>모든 사용자에게 읽기/쓰기 권한이 있습니다.</li><li>가상 네트워크 피어링이 필요하지 않습니다.</li><li>랩 VM뿐 아니라 모든 VM에서 액세스할 수 있습니다.</li><li>Linux를 사용하는 경우 학생이 스토리지 계정 키에 액세스할 수 있습니다.</li></ul> |
| [프라이빗 엔드포인트를 사용하는 Azure Files 공유](#azure-files-share) | <ul><li>모든 사용자에게 읽기/쓰기 권한이 있습니다.</li><li>가상 네트워크 피어링이 필요합니다.</li><li>스토리지 계정과 동일한 네트워크(또는 피어링된 네트워크)의 VM에만 액세스할 수 있습니다.</li><li>Linux를 사용하는 경우 학생이 스토리지 계정 키에 액세스할 수 있습니다.</li></ul> |
| [ID 기반 권한 부여를 사용하는 Azure Files](#azure-files-with-identity-based-authorization) | <ul><li>폴더나 파일에 대한 읽기 또는 읽기/쓰기 권한을 설정할 수 있습니다.</li><li>가상 네트워크 피어링이 필요합니다.</li><li>스토리지 계정이 Active Directory에 연결되어 있어야 합니다.</li><li>랩 VM이 도메인에 조인되어 있어야 합니다.</li><li>학생이 파일 공유에 연결하는 데 사용되지 않는 스토리지 계정 키입니다.</li></ul> |
| [NFS 볼륨을 사용하는 NetApp Files](#netapp-files-with-nfs-volumes) | <ul><li>볼륨에 대한 읽기 또는 읽기/쓰기 권한을 설정할 수 있습니다.</li><li>사용 권한은 학생 VM의 IP 주소를 사용하여 설정됩니다.</li><li>가상 네트워크 피어링이 필요합니다.</li><li>NetApp Files 서비스를 사용하려면 등록해야 할 수 있습니다.</li><li>Linux 전용입니다.</li></ul>

외부 스토리지 사용 비용은 Azure Lab Services 사용 비용에 포함되지 않습니다.  가격 책정에 대한 자세한 내용은 [Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/) 및 [Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/) 페이지를 참조하세요.

## <a name="azure-files-share"></a>Azure Files 공유

Azure Files 공유는 퍼블릭 또는 프라이빗 엔드포인트를 사용하여 액세스됩니다. 스토리지 계정 키를 암호로 사용하여 공유를 탑재합니다. 이 접근 방식에서는 모든 사용자가 파일 공유에 대한 읽기/쓰기 권한을 가집니다.

Azure Files 공유에 퍼블릭 엔드포인트를 사용하는 경우 다음 사항을 기억해야 합니다.

- 스토리지 계정의 가상 네트워크는 랩 계정으로 피어링할 필요가 없습니다.  파일 공유는 템플릿 VM이 게시되기 전에 언제든지 만들 수 있습니다.
- 사용자에게 스토리지 계정 키가 있는 경우 모든 머신에서 파일 공유에 액세스할 수 있습니다.  
- Linux 학생은 스토리지 계정 키를 볼 수 있습니다. Azure Files 공유를 탑재하기 위한 자격 증명은 Linux VM의 `{file-share-name}.cred`에 저장되고 sudo에서 읽을 수 있습니다. 학생에게는 Azure Lab Services VM에서 기본적으로 sudo 액세스 권한이 제공되므로 스토리지 계정 키를 읽을 수 있습니다. 스토리지 계정 엔드포인트가 퍼블릭인 경우 학생이 학생 VM 외부의 파일 공유에 대한 액세스 권한을 가져올 수 있습니다. 클래스가 종료된 후 스토리지 계정 키를 순환하고 프라이빗 파일 공유를 사용하는 것이 좋습니다.

Azure Files 공유에 프라이빗 엔드포인트를 사용하는 경우 다음 사항을 기억해야 합니다.

- 액세스는 개인 네트워크에서 시작되는 트래픽으로 제한되며 퍼블릭 인터넷을 통해 액세스할 수 없습니다. 개인 가상 네트워크의 VM 전용으로, 개인 가상 네트워크로 피어링된 네트워크의 VM 또는 개인 네트워크의 VPN에 연결된 머신은 파일 공유에 액세스할 수 있습니다.  
- Linux 학생은 스토리지 계정 키를 볼 수 있습니다. Azure Files 공유를 탑재하기 위한 자격 증명은 Linux VM의 `{file-share-name}.cred`에 저장되고 sudo에서 읽을 수 있습니다. 학생에게는 Azure Lab Services VM에서 기본적으로 sudo 액세스 권한이 제공되므로 스토리지 계정 키를 읽을 수 있습니다. 클래스가 종료된 후 스토리지 계정 키를 순환하는 것이 좋습니다.
- 이 방법을 사용하려면 파일 공유 가상 네트워크가 랩 계정에 피어링되어야 합니다. 랩을 만들려면 먼저 Azure Storage 계정의 가상 네트워크를 랩 계정의 가상 네트워크로 피어링해야 합니다.

> [!NOTE]
> 기본적으로 표준 파일 공유는 최대 5TiB까지 확장할 수 있습니다. 최대 100TiB에 걸쳐 있는 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure 파일 공유 만들기](../storage/files/storage-how-to-create-file-share.md)를 참조하세요.

Azure 파일 공유에 연결된 VM을 만들려면 다음 단계를 수행합니다.

1. [Azure Storage 계정](../storage/files/storage-how-to-create-file-share.md)을 만듭니다. **연결 방법** 페이지에서 **퍼블릭 엔드포인트** 또는 **프라이빗 엔드포인트** 를 선택합니다.
2. 비공개 방법을 선택한 경우 가상 네트워크에서 파일 공유에 액세스할 수 있도록 [프라이빗 엔드포인트](../private-link/tutorial-private-endpoint-storage-portal.md)를 만듭니다.
3. [Azure 파일 공유](../storage/files/storage-how-to-create-file-share.md)를 만듭니다. 퍼블릭 엔드포인트를 사용하는 경우 스토리지 계정의 퍼블릭 호스트 이름으로 파일 공유에 연결할 수 있습니다.  프라이빗 엔드포인트를 사용하는 경우 개인 IP 주소로 파일 공유에 연결할 수 있습니다.  
4. Azure 파일 공유를 템플릿 VM에 탑재합니다.
    - [Windows](../storage/files/storage-how-to-use-files-windows.md)
    - [Linux](../storage/files/storage-how-to-use-files-linux.md) 학생 VM에서의 탑재 문제를 방지하는 방법은 [Linux에서 Azure Files 사용](#use-azure-files-with-linux) 섹션을 참조하세요.
5. 템플릿 VM을 [게시](how-to-create-manage-template.md#publish-the-template-vm)합니다.

> [!IMPORTANT]
> Windows Defender 방화벽이 포트 445를 통해 나가는 SMB 연결을 차단하지 않는지 확인합니다. 기본적으로 Azure VM에는 SMB가 허용됩니다.

### <a name="use-azure-files-with-linux"></a>Linux에서 Azure Files 사용

Azure Files 공유를 탑재하는 기본 지침을 사용하는 경우 템플릿이 게시된 후에는 학생 VM에서 파일 공유가 사라진 것처럼 보입니다. 다음 수정된 스크립트는 이 문제를 해결합니다.  

퍼블릭 엔드포인트가 있는 파일 공유의 경우:
```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

프라이빗 엔드포인트가 있는 파일 공유의 경우:
```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_ip=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_ip/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Azure Files 공유를 `/mnt` 디렉터리에 탑재하는 템플릿 VM이 이미 게시된 경우 학생은 다음 중 하나를 수행할 수 있습니다.

- `/mnt`를 `/etc/fstab` 파일 맨 위에 탑재하도록 명령을 이동합니다.  
- `/mnt/{file-share-name}`을 `/prm-mnt/{file-share-name}`와 같이 다른 디렉터리에 탑재하도록 명령을 수정합니다.

학생은 `mount -a`를 실행하여 디렉터리를 다시 탑재해야 합니다.

일반적인 정보는 [Linux에서 Azure Files 사용](../storage/files/storage-how-to-use-files-linux.md)을 참조하세요.

## <a name="azure-files-with-identity-based-authorization"></a>ID 기반 권한 부여를 사용하는 Azure Files

Azure Files 공유는 다음 둘 모두에 해당하는 경우 Active Directory 인증을 사용하여 액세스할 수도 있습니다.

- 학생 VM은 도메인에 조인되어 있습니다.
- Active Directory 인증은 파일 공유를 호스트하는 [Azure Storage 계정에서 사용하도록 설정](../storage/files/storage-files-active-directory-overview.md)됩니다.  

네트워크 드라이브는 스토리지 계정의 키가 아닌 사용자의 ID를 사용하여 가상 머신에 탑재됩니다. 퍼블릭 또는 프라이빗 엔드포인트는 스토리지 계정에 대한 액세스는 제공합니다.

다음 중요 사항을 주의하세요.

- 디렉터리 또는 파일 수준에서 권한을 설정할 수 있습니다.
- 현재 사용자 자격 증명은 파일 공유를 인증하는 데 사용할 수 있습니다.

퍼블릭 엔드포인트의 경우 스토리지 계정의 가상 네트워크는 랩 계정에 피어링하지 않아도 됩니다. 템플릿 VM이 게시되기 전에 언제든지 파일 공유를 만들 수 있습니다.

프라이빗 엔드포인트의 경우:

- 액세스는 개인 네트워크에서 시작되는 트래픽으로 제한되며 퍼블릭 인터넷을 통해 액세스할 수 없습니다. 개인 가상 네트워크의 VM 전용으로, 개인 가상 네트워크로 피어링된 네트워크의 VM 또는 개인 네트워크의 VPN에 연결된 머신은 파일 공유에 액세스할 수 있습니다.  
- 이 방법을 사용하려면 파일 공유 가상 네트워크가 랩 계정에 피어링되어야 합니다. 랩을 만들려면 먼저 Azure Storage 계정의 가상 네트워크를 랩 계정의 가상 네트워크로 피어링해야 합니다.

Active Directory 인증이 사용하도록 설정된 Azure Files 공유를 만들고 랩 VM을 도메인에 가입하려면 다음 단계를 따르세요.

1. [Azure Storage 계정](../storage/files/storage-how-to-create-file-share.md)을 만듭니다.
2. 비공개 방법을 선택한 경우 가상 네트워크에서 파일 공유에 액세스할 수 있도록 [프라이빗 엔드포인트](../private-link/tutorial-private-endpoint-storage-portal.md)를 만듭니다. [프라이빗 DNS 영역](../dns/private-dns-privatednszone.md)을 만들거나 기존 영역을 사용합니다. Private Azure DNS 영역은 가상 네트워크 내에서 이름 확인을 제공합니다.
3. [Azure 파일 공유](../storage/files/storage-how-to-create-file-share.md)를 만듭니다.
4. 단계에 따라 ID 기반 권한 부여를 사용하도록 설정합니다. Active Directory 온프레미스를 사용하고 Azure AD(Azure Active Directory)와 동기화하는 경우 [Azure 파일 공유에 대해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증](../storage/files/storage-files-identity-auth-active-directory-enable.md)을 참조하세요. Azure AD만 사용하는 경우 [Azure Files에서 Azure Active Directory Domain Services 인증 사용](../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md)을 참조하세요.
    >[!IMPORTANT]
    >지침에 나열된 모든 필수 조건이 충족되는지 확인하려면 Active Directory 인스턴스를 관리하는 팀에 문의합니다.
5. Azure의 SMB 공유 권한 역할을 할당합니다. 각 역할에 부여된 사용 권한에 대한 자세한 내용은 [공유 수준 사용 권한](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)을 참조하세요.
    - 파일 공유 콘텐츠에 대한 권한을 설정하는 개인 또는 그룹에 **스토리지 파일 데이터 SMB 공유 상승된 기여자** 역할을 할당해야 합니다.
    - 파일 공유에서 파일을 추가하거나 편집해야 하는 학생에게 **스토리지 파일 데이터 SMB 공유 기여자** 역할을 할당해야 합니다.
    - 파일 공유에서 파일을 읽기만 하면 되는 학생에게 **스토리지 파일 데이터 SMB 공유 읽기 권한자** 역할을 할당해야 합니다.
6. 파일 공유를 위한 디렉터리 수준 및/또는 파일 수준 사용 권한을 설정합니다. 파일 공유에 대한 네트워크 액세스 권한이 있는 도메인 가입 시스템에서 권한을 설정해야 합니다. 디렉터리 수준 및/또는 파일 수준 권한을 수정하려면 Azure AD 자격 증명이 아닌 스토리지 키를 사용하여 파일 공유를 탑재합니다. 권한을 할당하려면 [Set-Acl](/powershell/module/microsoft.powershell.security/set-acl) PowerShell 명령을 사용하거나 Windows의 경우 [icacls](/windows-server/administration/windows-commands/icacls)를 사용합니다.
7. 스토리지 계정의 [가상 네트워크](how-to-connect-peer-virtual-network.md)를 랩 계정으로 피어링합니다.
8. [교실 랩을 만듭니다](how-to-manage-classroom-labs.md).
9. 네트워크 드라이브에 연결하려면 학생이 실행할 수 있는 템플릿 VM에 스크립트를 저장합니다. 예제 스크립트를 가져오려면 다음을 수행합니다.
    1. Azure Portal에서 스토리지 계정을 엽니다.
    1. **파일 서비스** 아래에서 **파일 공유** 를 선택합니다.
    1. 연결하려는 공유를 찾고 맨 오른쪽에 있는 줄임표 단추를 선택한 다음 **연결** 을 선택합니다.
    1. Windows, Linux 및 macOS에 대한 지침이 표시됩니다. Windows를 사용하는 경우 **인증 방법** 을 **Active Directory** 로 설정합니다.
    1. 예제의 코드를 복사하여 템플릿 컴퓨터의 Windows용 `.ps1` 파일 또는 Linux용 `.sh` 파일에 저장합니다.
10. 템플릿 컴퓨터에서 [학생 컴퓨터를 도메인에 가입](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage)하는 스크립트를 다운로드하여 실행합니다. `Join-AzLabADTemplate` 스크립트는 자동으로 [템플릿 VM을 게시](how-to-create-manage-template.md#publish-the-template-vm)합니다.  
    > [!NOTE]
    > 템플릿 컴퓨터가 도메인에 가입되어 있지 않습니다. 공유에 있는 파일을 보려면 강사가 학생 VM을 사용해야 합니다.
11. 파일 공유 경로가 지정되면 Windows를 사용하는 학생은 [파일 탐색기](../storage/files/storage-how-to-use-files-windows.md)를 사용하여 자격 증명으로 Azure Files 공유에 연결할 수 있습니다. 또는 학생들은 앞의 스크립트를 실행하여 네트워크 드라이브에 연결할 수 있습니다. Linux를 사용하는 학생의 경우 앞의 스크립트를 실행합니다.

## <a name="netapp-files-with-nfs-volumes"></a>NFS 볼륨을 사용하는 NetApp Files

[Azure NetApp Files ](https://azure.microsoft.com/services/netapp/)는 엔터프라이즈급의 고성능 유료 파일 스토리지 서비스입니다.

- 액세스 정책은 볼륨별로 설정할 수 있습니다.
- 권한 정책은 각 볼륨의 IP 기반입니다.
- 학생이 다른 학생이 액세스할 수 없는 자체 볼륨을 필요로 하는 경우 랩을 게시한 후에 권한 정책을 할당해야 합니다.
- Azure Lab Services 컨텍스트에서는 Linux 머신만 지원됩니다.
- 랩을 만들려면 **먼저** Azure NetApp Files 용량 풀의 가상 네트워크를 랩 계정의 가상 네트워크로 피어링해야 합니다.

Azure Lab Services에서 Azure NetApp Files 공유를 사용하려면:

1. NetApp Files 용량 풀 및 하나 이상의 NFS 볼륨을 만들려면 [Azure NetApp Files 및 NFS 볼륨 설정](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)을 참조하세요. 서비스 수준에 대한 자세한 내용은 [Azure NetApp Files의 서비스 수준](../azure-netapp-files/azure-netapp-files-service-levels.md)을 참조하세요.
2. 랩 계정으로 NetApp Files 용량 풀의 [가상 네트워크를 피어링](how-to-connect-peer-virtual-network.md)합니다.
3. [교실 랩을 만듭니다](how-to-manage-classroom-labs.md).
4. 템플릿 VM에서 NFS 파일 공유를 사용하는 데 필요한 구성 요소를 설치합니다.
    - Ubuntu:

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS:

        ```bash
        sudo yum install nfs-utils
        ```

5. 템플릿 VM에서 다음 스크립트를 `mount_fileshare.sh`로 저장하여 [NetApp Files 공유를 탑재](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)합니다. 용량 풀의 탑재 대상 IP 주소를 `capacity_pool_ipaddress` 변수에 할당합니다. 적절한 값을 찾기 위해 볼륨의 탑재 지침을 가져옵니다. 스크립트에는 NetApp Files 볼륨의 경로 이름이 필요합니다. 사용자가 스크립트를 실행할 수 있도록 하려면 `chmod u+x mount_fileshare.sh`를 실행합니다.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ might cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

6. 모든 학생이 동일한 NetApp Files 볼륨에 대한 액세스 권한을 공유하는 경우 게시하기 전에 템플릿 컴퓨터에서 `mount_fileshare.sh` 스크립트를 실행할 수 있습니다. 각 학생이 고유한 볼륨을 얻는 경우 나중에 학생이 실행할 스크립트를 저장합니다.
7. 템플릿 VM을 [게시](how-to-create-manage-template.md#publish-the-template-vm)합니다.
8. 파일 공유에 대한 [정책을 구성](../azure-netapp-files/azure-netapp-files-configure-export-policy.md)합니다. 내보내기 정책은 단일 VM 또는 여러 VM이 볼륨에 액세스하도록 허용할 수 있습니다. 읽기 전용 또는 읽기/쓰기 액세스 권한을 부여할 수 있습니다.
9. 학생은 VM을 시작하고 스크립트를 실행하여 파일 공유를 탑재해야 합니다. 스크립트를 한 번만 실행하면 됩니다. 명령은 다음과 같이 표시됩니다. `./mount_fileshare.sh myvolumename`.

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)
