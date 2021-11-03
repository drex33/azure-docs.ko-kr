---
title: Azure HPC Cache 데이터 수집 - 병렬 복사 스크립트
description: 병렬 복사 스크립트를 사용하여 Azure HPC Cache에서 BLOB 스토리지 대상으로 데이터를 이동하는 방법
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: femila
ms.openlocfilehash: 0285a57fe0ebecce2f55bdb3d6723dd47ac0070a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087783"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Azure HPC Cache 데이터 수집 - 병렬 복사 스크립트 메서드

이 문서에서는 ``parallelcp`` 스크립트를 만들고 이를 사용하여 Azure HPC Cache에서 사용할 BLOB 스토리지 컨테이너로 데이터를 이동하는 방법에 대한 지침을 제공합니다.

Azure HPC Cache의 BLOB 스토리지로 데이터를 이동하는 방법에 대한 자세한 내용은 [Azure BLOB 스토리지로 데이터 이동](hpc-cache-ingest.md)을 참조하세요.

## <a name="create-the-parallelcp-script"></a>병렬 스크립트 만들기

아래 스크립트는 `parallelcp` 실행 파일을 추가합니다. (이 스크립트는 Ubuntu에서 사용하도록 설계되었으므로, 다른 배포판을 사용하는 경우 ``parallel``을 별도로 설치해야 합니다.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>병렬 복사 예제

다음 예제에서는 Azure HPC Cache의 원본 파일을 사용하여 ``glibc``를 컴파일하는 병렬 복사 스크립트를 사용합니다.

원본 파일은 Azure HPC Cache 탑재 지점에 캐시되고, 개체 파일은 로컬 하드 드라이브에 저장됩니다.

이 예제에서는 ``-j`` 및 ``make`` 옵션이 있는 병렬 복사 스크립트를 사용하여 병렬화를 얻습니다.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
