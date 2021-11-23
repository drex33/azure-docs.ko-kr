---
title: 클라이언트 도구 설치
description: Azure Data Studio에 대한 azdata, kubectl, Azure CLI, psql, Azure Data Studio(참가자) 및 Arc 확장을 설치합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 6f45667fa4f7aed4f5081690c8b84cc3ad77ddb5
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132956507"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Azure Arc 지원 데이터 서비스 배포 및 관리를 위한 클라이언트 도구 설치

이 문서에서는 Azure Arc 사용 데이터 서비스를 관리 하는 도구를 설치 하는 리소스를 안내 합니다.

> [!IMPORTANT]
> 새 릴리스로 업데이트하는 경우 최신 버전의 Azure Data Studio, Azure Data Studio용 Azure Arc 확장, Azure(`az`) CLI(명령줄 인터페이스), [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]으로 업데이트됩니다.
>
> [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)] 

[ `arcdata` Azure CLI ( `az` )에 대 한 확장](reference/reference-az-arcdata-dc.md) 은 `azdata` Azure Arc 사용 데이터 서비스를 대체 합니다.

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Azure Arc 지원 데이터 서비스를 만들고 관리하기 위한 도구

다음 표에서는 Azure Arc 지원 데이터 서비스를 만들고 관리하는 데 필요한 일반적인 도구와 이러한 도구를 설치하는 방법을 보여 줍니다.

| 도구 | 필수 | Description | 설치 |
|---|---|---|---|
| Azure CLI(`az`)<sup>1</sup> | Yes | Azure 서비스를 관리하기 위한 최신 명령줄 인터페이스입니다. 일반적으로 Azure 서비스를 관리 하는 데 사용 되며 간접적으로 연결 된 모드 (지금 사용 가능) 및 직접 연결 된 모드 (곧 사용 가능)에 대 한 CLI 또는 스크립트를 사용 하 여 특히 Azure Arc 사용 데이터 서비스를 관리 합니다. ([추가 정보](/cli/azure/)) | [설치](/cli/azure/install-azure-cli) |
| Azure(`az`) CLI용 `arcdata` 확장 | Yes | Azure CLI ()에 대 한 확장으로 Azure Arc 사용 데이터 서비스를 관리 하기 위한 명령줄 도구입니다. `az` | [설치](install-arcdata-extension.md) |
| Azure Data Studio | 예 | Azure SQL, SQL Server, PostrgreSQL 및 MySQL을 비롯한 다양한 데이터베이스에 연결하고 쿼리하는 데 사용할 수 있는 풍부한 환경 도구입니다. Azure Data Studio에 대한 확장은 Azure Arc 지원 데이터 서비스에 대한 관리 환경을 제공합니다. | [설치](/sql/azure-data-studio/download-azure-data-studio) |
| Azure Data Studio용 Azure Arc 확장 | Yes | Azure Arc 지원 데이터 서비스에 대한 관리 환경을 제공하는 Azure Data Studio에 대한 확장입니다.| Azure Data Studio의 확장 갤러리에서 설치합니다.|
| Azure Data Studio의 PostgreSQL 확장 | 아니요 | PostgreSQL에 대한 관리 기능을 제공하는 Azure Data Studio용 PostgreSQL 확장입니다. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Azure Data Studio의 확장 갤러리에서 설치합니다.|
| Kubernetes CLI(kubectl)<sup>2</sup> | Yes | Kubernetes 클러스터 관리를 위한 명령줄 도구([추가 정보](https://kubernetes.io/docs/tasks/tools/install-kubectl/)) | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | 일부 샘플 스크립트에 필요합니다. | URL을 사용하여 데이터를 전송하기 위한 명령줄 도구입니다. | [Windows](https://curl.haxx.se/windows/) \| Linux: curl 패키지 설치 |
| oc | Red Hat OpenShift 및 Azure Redhat OpenShift 배포에 필요합니다. |`oc`는 OpenShift CLI(명령줄 인터페이스)입니다. | [CLI 설치](https://docs.openshift.com/container-platform/4.6/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> Azure CLI 버전 2.26.0 이상을 사용해야 합니다. 필요한 경우 `az --version`을 실행하여 버전을 찾습니다.

<sup>2</sup> `kubectl` 버전 1.19 이상을 사용해야 합니다. 또한 `kubectl` 버전은 Kubernetes 클러스터의 바로 이전 또는 이후 부 버전이어야 합니다. `kubectl` 클라이언트에서 특정 버전을 설치하려는 경우 [Install `kubectl` binary via curl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl)(curl을 통해 kubectl 이진 설치)를 참조하세요(Windows 10에서는 Windows PowerShell이 아닌 cmd.exe를 사용하여 curl 실행).

<sup>3</sup> PowerShell을 사용하는 경우 curl은 Invoke-WebRequest cmdlet의 별칭입니다.

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 데이터 서비스 배포 계획](plan-azure-arc-data-services.md)
