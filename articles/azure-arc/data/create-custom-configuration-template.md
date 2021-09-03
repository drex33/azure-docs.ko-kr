---
title: 사용자 지정 구성 템플릿 만들기
description: 사용자 지정 구성 템플릿 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dinethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e7d5d470fd967c9f350fddaae2032085caf17c07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566820"
---
# <a name="create-custom-configuration-templates"></a>사용자 지정 구성 템플릿 만들기

이 문서에서는 Azure Arc 지원 데이터 컨트롤러에 대한 사용자 지정 구성 템플릿을 만드는 방법을 설명합니다. 

간접 연결 모드에서 데이터 컨트롤러를 배포하는 동안 필요한 매개 변수 중 하나는 `az arcdata dc create --profile-name` 매개 변수입니다. 현재 사용 가능한 기본 제공 프로필 목록은 쿼리를 실행하여 찾을 수 있습니다.

```azurecli
az arcdata dc config list
```
이러한 프로필은 Azure Arc 지원 데이터 컨트롤러(Docker 레지스트리 및 리포지토리 설정, 데이터 및 로그의 스토리지 클래스, 데이터 및 로그의 스토리지 크기, 보안, 서비스 유형 등)에 대한 다양한 설정이 있는 템플릿 JSON 파일이며 사용자 환경에 맞게 사용자 지정할 수 있습니다. 

그러나 경우에 따라 요구 사항을 충족하도록 이러한 구성 템플릿을 사용자 지정하고 `--profile-name` 매개 변수를 사용하여 사전 구성된 구성 템플릿을 전달하는 대신 `--path` 매개 변수를 사용하여 사용자 지정된 구성 템플릿을 `az arcdata dc create` 명령에 전달할 수 있습니다.

## <a name="create-customjson-file"></a>custom.json 파일 만들기

`az arcdata dc config init`를 실행하여 Kubernetes 클러스터 배포를 기반으로 사전 정의된 설정의 control.json 파일을 시작합니다.
예를 들어, 현재 작업 디렉터리의 `custom`이라는 하위 디렉터리에 있는 `azure-arc-kubeadm` 템플릿을 기반으로 하는 Kubernetes 클러스터용 템플릿 control.json 파일은 다음과 같이 만들 수 있습니다.

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path custom
```
만들어진 control.json 파일은 Visual Studio Code와 같은 편집기에서 편집하여 환경에 적합한 설정을 사용자 지정할 수 있습니다.

## <a name="use-custom-controljson-file-to-deploy-azure-arc-enabled-data-controller-using-azure-cli-az"></a>사용자 지정 control.json 파일을 사용하여 Azure CLI(az)를 사용하여 Azure Arc 지원 데이터 컨트롤러 배포

템플릿 파일을 만들면 Azure Arc 지원 데이터 컨트롤러 만들기 명령 중에 다음과 같이 파일을 적용할 수 있습니다.

```azurecli
az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect  --k8s-namespace <namespace> --use-k8s

#Example:
#az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription ID> --resource-group my-resource-group --location eastus --connectivity-mode indirect --k8s-namespace <namespace> --use-k8s
```

## <a name="use-custom-controljson-file-for-deploying-azure-arc-data-controller-using-azure-portal"></a>Azure Portal을 사용하여 Azure Arc 데이터 컨트롤러를 배포하기 위해 사용자 지정 control.json 파일 사용

Azure Arc 데이터 컨트롤러 만들기 화면에서 사용자 지정 템플릿 아래의 "사용자 지정 템플릿 구성"을 선택합니다. 그러면 블레이드가 호출되어 사용자 지정 설정을 제공합니다. 이 블레이드에서 다양한 설정 값을 입력하거나 미리 구성된 control.json 파일을 직접 업로드할 수 있습니다. 

값이 올바른지 확인한 후 적용을 클릭하여 Azure Arc 데이터 컨트롤러 배포를 진행합니다.

## <a name="next-steps"></a>다음 단계

[데이터 컨트롤러 배포 - 직접 연결 모드(필수 구성 요소)](create-data-controller-direct-prerequisites.md)

[Azure Arc 데이터 컨트롤러 만들기(CLI)](create-data-controller-direct-cli.md)
