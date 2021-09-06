---
title: 패키지 관리를 위한 사용자 지정 Conda 채널 만들기
description: 패키지 관리를 위한 사용자 지정 Conda 채널을 만드는 방법을 알아봅니다.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 937bfbf1ab1f874c6582b005ee0f7376ecf46235
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529432"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>패키지 관리를 위한 사용자 지정 Conda 채널 만들기 
Python 패키지를 설치하는 경우 Conda 패키지 관리자는 채널을 사용하여 패키지를 찾습니다. 다양한 이유로 사용자 지정 Conda 채널을 만들어야 할 수 있습니다. 예를 들어 다음을 확인할 수 있습니다.

- 작업 영역에서 데이터 반출이 보호되고 아웃바운드 연결이 차단되는지 확인  
- 공용 리포지토리에 업로드하지 않으려는 패키지가 있는지 확인
- 작업 영역 내 사용자를 위한 대체 리포지토리를 설정하려고 합니다.

이 문서에서는 Azure Data Lake Storage 계정 내에서 사용자 지정 Conda 채널을 만드는 데 도움이 되는 단계별 가이드를 제공합니다.

## <a name="set-up-your-local-machine"></a>로컬 컴퓨터 설정

1. 로컬 컴퓨터에 Conda를 설치합니다. [Azure Synapse Spark 런타임](./apache-spark-version-support.md)을 참조하여 동일한 런타임에서 사용되는 Conda 버전을 식별할 수 있습니다.
   
2. 사용자 지정 채널을 만들려면 conda-build를 설치합니다.
```
conda install conda-build
```
3. 제공하려는 플랫폼에 대한 모든 패키지를 구성합니다. 이 예에서는 로컬 컴퓨터에 Anaconda 아카이브를 설치합니다.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3  
```

4. Azure Synapse 런타임에서 사용할 수 있는 것과 유사한 환경을 만들기 위해 [이 템플릿](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml)을 다운로드할 수 있습니다. 템플릿과 실제 Azure Synapse 환경 간에 약간의 차이가 있을 수 있습니다. 다운로드가 완료되면 다음 명령을 실행할 수 있습니다.
```
apt-get -yq install gcc g++
conda env update --prune -f base_environment.yml
```

## <a name="mount-the-storage-account-onto-your-machine"></a>컴퓨터에 스토리지 계정 탑재
다음으로 Azure Data Lake Storage Gen2 계정을 로컬 컴퓨터에 탑재합니다. 이 프로세스는 WASB 계정으로도 수행할 수 있습니다. 그러나 여기서는 ADLSg2 계정에 대한 예를 살펴보겠습니다. 
 
로컬 컴퓨터에 스토리지 계정을 탑재하는 방법에 대한 자세한 내용은 [이 페이지](https://github.com/Azure/azure-storage-fuse#blobfuse )를 참조하세요. 

1. Microsoft 제품용 Linux 소프트웨어 리포지토리에서 Blobfuse를 설치할 수 있습니다.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<storage-account-name>
export AZURE_STORAGE_SAS_TOKEN="<SAS>" 
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net
```

2. 탑재 지점(```mkdir /path/to/mount```)을 만들고 Blobfuse로 Blob 컨테이너를 탑재합니다. 이 예에서는 **mycontainer** 변수에 **privatechannel** 값을 사용하겠습니다.
   
```
sudo mkdir /home/trusted-service-user/privatechannel 
sudo mkdir -p /mnt/blobfusetmp 
blobfuse /home/trusted-service-user/privatechannel --container-name=privatechannel --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo chown trusted-service-user /mnt/blobfusetmp 
```
## <a name="create-the-channel"></a>채널 만들기
다음 단계에서는 사용자 지정 Conda 채널을 만듭니다.

1. 로컬 컴퓨터에서 사용자 지정 채널에 대한 모든 패키지를 구성할 디렉터리를 만듭니다. https://repo.anaconda.com/pkgs/main/linux-64/ 의 모든 ```tar.bz2``` 패키지를 하위 디렉터리에 구성합니다. 모든 종속 tar.bz2 패키지도 포함해야 합니다.
   
```

cd ~/privatechannel/ 
mkdir -P channel/linux64 

<Add all .tar.bz2 from https://repo.anaconda.com/pkgs/main/linux-64/> 
// Note: Add all dependent .tar.bz2 as well 

cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 
conda index channel/noarch 
conda index channel/linux-64 
conda index channel
```

2. 이제 ```privatechannel/channel``` 디렉터리가 생성되었을 스토리지 계정을 확인할 수 있습니다.

>[!Note]
> Conda는 컨테이너에 연결된 SAS 토큰을 인식하지 못합니다. 따라서 "privatechannel" 컨테이너를 공용 액세스로 표시해야 합니다.


자세한 내용은 [Conda 사용자 가이드를 방문](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html)하여 사용자 지정 채널 만들기에 대한 내용을 참조할 수도 있습니다. 

## <a name="storage-account-permissions"></a>스토리지 계정 권한
이제 스토리지 계정에 대한 권한의 유효성을 검사해야 합니다. 이러한 권한을 설정하기 위해 사용자 지정 채널을 만들 경로로 이동합니다. 그런 다음 ```privatechannel```에 대한 읽기, 나열 및 실행 권한이 있는 SAS 토큰을 만듭니다. 

이제 채널 이름은 이 프로세스에서 생성된 Blob SAS URL입니다.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>샘플 Conda 환경 구성 파일 만들기
마지막으로 샘플 Conda ```environment.yml``` 파일을 만들어 설치 프로세스를 확인합니다. 데이터 유출 방지가 활성화된 작업 영역에 있는 경우 환경 파일에 ``nodefaults`` 채널을 지정해야 합니다.

다음은 Conda 구성 파일 예입니다.
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
샘플 Conda 파일을 만든 후에는 가상 Conda 환경을 만들 수 있습니다. 다음 명령을 실행하여 로컬로 확인할 수 있습니다.

```
conda env create –file sample.yml  
source activate env 
conda list 
```
이제 사용자 지정 채널을 확인했으므로 [Python 풀 관리](./apache-spark-manage-python-packages.md) 프로세스를 사용하여 Apache Spark 풀의 라이브러리를 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 기본 라이브러리 보기: [Apache Spark 버전 지원](apache-spark-version-support.md)
- Python 패키지 관리: [Python 패키지 관리](./apache-spark-manage-python-packages.md)

