---
title: Debian Linux VHD 준비
description: Azure에서 배포할 Debian VHD 이미지를 만드는 방법을 알아봅니다.
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 6/3/2021
ms.author: srijangupta
ms.openlocfilehash: 4e9d7eec14e994d88f4212df4d0e848de1ea6476
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689775"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Azure용 Debian VHD 준비

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

## <a name="prerequisites"></a>필수 구성 요소
이 섹션에서는 [Debian 웹 사이트](https://www.debian.org/distrib/) 에서 다운로드한 .iso 파일에서 Debian Linux 운영 체제를 가상 하드 디스크에 설치했다고 가정합니다. .vhd 파일을 만드는 여러 도구가 있으며 Hyper-V가 한 가지 예입니다. Hyper-V 사용에 대한 자세한 내용은 [Hyper-V 역할 설치 및 Virtual Machine 구성](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))을 참조하세요.

## <a name="installation-notes"></a>설치 참고 사항
* Azure용 Linux를 준비하는 방법에 대한 추가 팁은 [일반 Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes)을 참조하세요.
* 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 **convert-vhd** cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.
* Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 원하는 경우에는 데이터 디스크에서 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) 또는 [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)를 사용할 수 있습니다.
* OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Azure Linux 에이전트를 구성할 수 있습니다. 자세한 내용은 아래 단계에서 찾을 수 있습니다.
* Azure의 모든 VHD는 가상 크기가 1MB 단위로 조정되어야 합니다. 원시 디스크에서 VHD로 변환할 때 변환하기 전에 원시 디스크 크기가 1MB의 배수인지 확인해야 합니다. 자세한 내용은 [Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes)을 참조하세요.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Azure-Manage를 사용하여 Debian VHD를 만듭니다.
[redativ](https://www.credativ.com/)의 [azure-manage](https://github.com/credativ/azure-manage) 스크립트처럼 Azure용 Debian VHD를 생성하는 데 사용할 수 있는 도구가 있습니다. 권장되는 방법과 이미지를 처음부터 새로 만드는 방법을 비교한 것입니다. 예를 들어 Debian 8 VHD를 만들기 위해 다음 명령을 실행하여 `azure-manage`(및 종속성)를 다운로드하고 `azure_build_image` 스크립트를 실행합니다.

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="prepare-a-debian-image-for-azure"></a>Azure용 Debian 이미지 준비

[FAI 클라우드 이미지 작성기](https://salsa.debian.org/cloud-team/debian-cloud-images)를 사용하여 기본 Azure Debian Cloud 이미지를 만들 수 있습니다.

(Debian Cloud 이미지 리포지토리에서 다음 git clone 및 apt install 명령을 가져왔습니다.) 리포지토리를 복제하고 종속성을 설치하여 시작합니다.

```
$ git clone https://salsa.debian.org/cloud-team/debian-cloud-images.git
$ sudo apt install --no-install-recommends ca-certificates debsums dosfstools \
    fai-server fai-setup-storage make python3 python3-libcloud python3-marshmallow \
    python3-pytest python3-yaml qemu-utils udev
$ cd ./debian-cloud-images
```

(선택 사항) 필드 스크립트(예: 셸 스크립트)를 `./config_space/scripts/AZURE`에 추가하여 빌드를 사용자 지정합니다.



## <a name="an-example-of-a-script-to-customize-the-image-is"></a>이미지를 사용자 지정하는 스크립트의 예는 다음과 같습니다.

```
$ mkdir -p ./config_space/scripts/AZURE
$ cat > ./config_space/scripts/AZURE/10-custom <<EOF
#!/bin/bash

\$ROOTCMD bash -c "echo test > /usr/local/share/testing"
EOF
$ sudo chmod 755 ./config_space/scripts/AZURE/10-custom
```

이미지를 사용자 지정하려는 모든 명령에 `chroot $target`으로 별칭이 지정되므로 `$ROOTCMD`로 접두사를 지정하는 것이 중요합니다.


## <a name="build-the-azure-debian-10-image"></a>Azure Debian 10 이미지를 빌드합니다.

```
$ make image_buster_azure_amd64
```


이렇게 하면 현재 디렉터리(특히 `image_buster_azure_amd64.raw` 이미지 파일)에 몇 개의 파일이 출력됩니다.

원시 이미지를 Azure용 VHD로 변환하려면 다음을 수행하면 됩니다.

```
rawdisk="image_buster_azure_amd64.raw"
vhddisk="image_buster_azure_amd64.vhd"

MB=$((1024*1024))
size=$(qemu-img info -f raw --output json "$rawdisk" | \
gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

rounded_size=$(((($size+$MB-1)/$MB)*$MB))
rounded_size_adjusted=$(($rounded_size + 512))

echo "Rounded Size Adjusted = $rounded_size_adjusted"

sudo qemu-img resize "$rawdisk" $rounded_size
qemu-img convert -f raw -o subformat=fixed,force_size -O vpc "$rawdisk" "$vhddisk"
```


이렇게 하면 Azure 디스크에 성공적으로 복사할 수 있도록 크기가 반올림된 VHD `image_buster_azure_amd64.vhd`가 만들어집니다.

이제 이 이미지에 대한 Azure 리소스를 만들어야 합니다. 이는 `$rounded_size_adjusted` 변수를 사용하므로 위와 동일한 셸 프로세스 내에서 가져와야 합니다.

```
az group create -l $LOCATION -n $RG

az disk create \
    -n $DISK \
    -g $RG \
    -l $LOCATION \
    --for-upload --upload-size-bytes "$rounded_size_adjusted" \
    --sku standard_lrs --hyper-v-generation V1

ACCESS=$(az disk grant-access \
    -n $DISK -g $RG \
    --access-level write \
    --duration-in-seconds 86400 \
    --query accessSas -o tsv)

azcopy copy "$vhddisk" "$ACCESS" --blob-type PageBlob

az disk revoke-access -n $DISK -g $RG
az image create \
    -g $RG \
    -n $IMAGE \
    --os-type linux \
    --source $(az disk show \
        -g $RG \
        -n $DISK \
        --query id -o tsv)
az vm create \
    -g $RG \
    -n $VM \
    --ssh-key-value $SSH_KEY_VALUE \
    --public-ip-address-dns-name $VM \
    --image $(az image show \
        -g $RG \
        -n $IMAGE \
        --query id -o tsv)
```


>[!Note]
> 로컬 컴퓨터에서 Azure 디스크로의 대역폭으로 인해 azcopy로 업로드를 처리하는 데 오랜 시간이 소요되는 경우 Azure VM 점프 상자를 사용하여 프로세스의 속도를 높일 수 있습니다. 이 작업을 수행하는 방법은 다음과 같습니다.
>
>1. 로컬 컴퓨터(`tar -czvf ./image_buster_azure_amd64.vhd.tar.gz ./image_buster_azure_amd64.vhd`)에서 VHD의 tarball을 만듭니다.
>2. Azure Linux VM(선택한 배포판)을 만듭니다. 추출한 VHD를 보관할 만큼 충분한 디스크를 사용하여 만들어야 합니다.
>3. Azure Linux VM에 azcopy 유틸리티를 다운로드합니다. [여기](../../storage/common/storage-use-azcopy-v10.md#download-azcopy)에서 검색할 수 있습니다.
>4. tarball을 VM(`scp ./image_buster_azure_amd64.vhd.tar.gz <vm>:~`)에 복사합니다.
>5. VM에서 VHD를 추출합니다. `tar -xf ./image_buster_azure_amd64.vhd.tar.gz`(파일 크기를 지정하는 데 약간의 시간이 소요됩니다).
>6. 마지막으로 VM에서 `azcopy`(위의 명령)를 사용하여 VHD를 Azure Disk에 복사합니다.
