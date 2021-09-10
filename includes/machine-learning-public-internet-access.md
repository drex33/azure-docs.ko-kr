---
title: 포함 파일
description: 포함 파일
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 07/21/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: b0b46da23de802d9f26e53d7b3acc96f166e78d0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443397"
---
Azure Machine Learning을 사용하려면 공용 인터넷에 대한 인바운드 및 아웃바운드 액세스가 모두 필요합니다. 다음 표에서는 필요한 액세스 권한과 용도에 대한 개요를 제공합니다. 모든 항목에 대한 __프로토콜__ 은 __TCP__ 입니다. `.region`으로 끝나는 서비스 태그의 경우 `region`을 작업 영역이 포함된 Azure 지역으로 바꿉니다. 예: `Storage.westus`

| Direction | 포트 | 서비스 태그 | 목적 |
| ----- |:-----:| ----- | ----- |
| 인바운드 | 29876-29877 | BatchNodeManagement | Azure Machine Learning 컴퓨팅 인스턴스 및 컴퓨팅 클러스터. |
| 인바운드 | 44224 | AzureMachineLearning | Azure Machine Learning 컴퓨팅 인스턴스. |
| 아웃바운드 | * | AzureActiveDirectory | Azure Active Directory 인증 |
| 아웃바운드 | 443 | AzureMachineLearning | Azure Machine Learning입니다. |
| 아웃바운드 | 443 | AzureResourceManager | Azure Resource Manager입니다. |
| 아웃바운드 | 443 | Storage.region | Azure Storage 계정 |
| 아웃바운드 | 443 | AzureFrontDoor.FrontEnd</br>* Azure 중국에서는 필요하지 않습니다. | Azure Front Door입니다. | 
| 아웃바운드 | 443 | ContainerRegistry.region | Azure Container Registry입니다. 사용자 지정 Docker 이미지를 사용하는 경우에만 필요합니다. Microsoft에서 제공하는 기본 이미지에 대한 약간의 수정(예: 추가 패키지)을 포함합니다. |
| 아웃바운드 | 443 | MicrosoftContainerRegistry.region | Microsoft에서 제공하는 Docker 이미지를 사용하고 사용자 관리 종속성을 사용하도록 설정하는 경우에만 필요합니다. |

> [!TIP]
> 서비스 태그 대신 IP 주소가 필요한 경우 다음 옵션 중 하나를 사용합니다.
> * [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)에서 목록을 다운로드합니다.
> * Azure CLI [az network list-service-tags](/cli/azure/network#az_network_list_service_tags) 명령을 사용합니다.
> * Azure PowerShell [Get-AzNetworkServiceTag](/powershell/module/az.network/get-aznetworkservicetag) 명령을 사용합니다.
> 
> IP 주소는 주기적으로 변경될 수 있습니다.

기계 학습 프로젝트에 필요한 패키지를 설치하기 위해 Visual Studio Code 및 타사 사이트에 대한 __아웃바운드__ 트래픽을 허용해야 할 수도 있습니다. 다음 표에서는 기계 학습에 일반적으로 사용되는 리포지토리를 나열합니다.

| 호스트 이름 | 용도 |
| ----- | ----- |
| **anaconda.com**</br>**\*.anaconda.com** | 기본 패키지를 설치하는 데 사용됩니다. |
| **\*.anaconda.org** | 리포지토리 데이터를 가져오는 데 사용됩니다. |
| **pypi.org** | 기본 인덱스에서 종속성을 나열하는 데 사용되며 (있는 경우) 사용자 설정에서 인덱스를 덮어쓰지 않습니다. 인덱스를 덮어쓰는 경우 **\*.pythonhosted.org** 도 허용해야 합니다. |
| **cloud.r-project.org** | R 개발용 CRAN 패키지를 설치할 때 사용됩니다. |
| **\*pytorch.org** | PyTorch를 기반으로 하는 일부 예제에서 사용됩니다. |
| **\*.tensorflow.org** | Tensorflow를 기반으로 하는 일부 예제에서 사용됩니다. |
| **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | 설정 스크립트를 통해 컴퓨팅 인스턴스에 설치된 VS Code 서버 비트를 검색하는 데 사용됩니다.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | 컴퓨팅 인스턴스에 설치된 websocket 서버 비트를 검색하는 데 사용됩니다. websocket 서버는 Visual Studio Code 클라이언트(데스크톱 애플리케이션)의 요청을 컴퓨팅 인스턴스에서 실행 중인 Visual Studio Code 서버로 전송하는 데 사용됩니다.|