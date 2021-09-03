---
title: 오프라인 배포
description: 오프라인 배포를 사용하면 Microsoft Container Registry에서 끌어오지 않고 프라이빗 컨테이너 레지스트리에서 컨테이너 이미지를 끌어올 수 있습니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e81ccc8ef133cdf7c454a7a50610a1bfdf2be442
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731273"
---
# <a name="offline-deployment-overview"></a>오프라인 배포 개요

일반적으로 Azure Arc 데이터 컨트롤러, SQL 관리형 인스턴스, PostgreSQL 하이퍼스케일 서버 그룹을 만드는 데 사용되는 컨테이너 이미지는 MCR(Microsoft Container Registry)에서 직접 가져옵니다. 경우에 따라 배포하는 환경이 Microsoft Container Registry에 연결되지 않습니다.  이와 같은 상황에서는 Microsoft Container Registry에 액세스할 수 ‘있는’ 컴퓨터를 사용하여 컨테이너 이미지를 끌어온 다음, 태그를 지정하고, Azure Arc 지원 데이터 서비스를 배포할 환경에 연결할 수 ‘있는’ 프라이빗 컨테이너 레지스트리로 푸시하면 됩니다. 

Azure Arc 지원 데이터 서비스에 대한 월별 업데이트가 제공되며 많은 수의 컨테이너 이미지가 있으므로 스크립트를 사용하여 컨테이너 이미지를 끌어오고, 태그를 지정하고, 프라이빗 컨테이너 레지스트리로 푸시하는 이 프로세스를 수행하는 것이 가장 좋습니다.  스크립트는 자동화하거나 수동으로 실행할 수 있습니다.

[샘플 스크립트](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py)는 Azure Arc GitHub 리포지토리에서 찾을 수 있습니다.

> [!NOTE]
> 이 스크립트를 사용하려면 python과 [Docker CLI](https://docs.docker.com/install/)를 설치해야 합니다.

해당 스크립트는 다음 정보를 대화형으로 표시합니다.  또는 대화형 프롬프트 없이 스크립트를 실행하려면 스크립트를 실행하기 전에 해당 환경 변수를 설정할 수 있습니다.

|prompt|환경 변수|참고|
|---|---|---|
|원본 컨테이너 레지스트리 입력 - ENTER 키를 누르면 `mcr.microsoft.com` 사용|SOURCE_DOCKER_REGISTRY|일반적으로 Microsoft Container Registry 이미지를 끌어오지만 다른 레지스트리를 사용하여 프라이빗 미리 보기에 참여하는 경우, 미리 보기 프로그램의 일환으로 제공된 정보를 사용할 수 있습니다.|
|원본 컨테이너 레지스트리 리포지토리 입력 - ENTER 키를 누르면 `arcdata` 사용|SOURCE_DOCKER_REPOSITORY|Microsoft Container Registry에서 끌어오면 리포지토리는 `arcdata`가 됩니다.|
|원본 컨테이너 레지스트리용 사용자 이름 입력 - ENTER 키를 누르면 아무것도 사용하지 않음|SOURCE_DOCKER_USERNAME|로그인이 필요한 원본에서 컨테이너 이미지를 끌어오는 경우에만 값을 제공합니다.  Microsoft Container Registry에는 로그인이 필요하지 않습니다.|
|원본 컨테이너 레지스트리용 암호 입력 - ENTER 키를 누르면 아무것도 사용하지 않음|SOURCE_DOCKER_PASSWORD|로그인이 필요한 원본에서 컨테이너 이미지를 끌어오는 경우에만 값을 제공합니다.  Microsoft Container Registry에는 로그인이 필요하지 않습니다. 마스킹된 암호 프롬프트입니다.  입력하거나 붙여넣는 경우에 암호가 보이지 않습니다.|
|원본 이미지용 컨테이너 이미지 태그 입력 - ENTER 키를 누르면 `<current monthly release tag>` 사용|SOURCE_DOCKER_TAG|기본 태그 이름은 Microsoft Container Registry에서 현재 릴리스의 월과 연도를 반영하도록 매월 업데이트됩니다.|
|대상 컨테이너 레지스트리 DNS 이름 또는 IP 주소 입력|TARGET_DOCKER_REGISTRY|대상 레지스트리 DNS 이름 또는 IP 주소입니다.  이미지 푸시 ‘대상’이 되는 레지스트리입니다.|
|대상 컨테이너 레지스트리 리포지토리 입력|TARGET_DOCKER_REPOSITORY|이미지를 푸시할 대상 레지스트리의 리포지토리입니다.|
|대상 컨테이너 레지스트리용 사용자 이름 입력 - ENTER 키를 누르면 아무것도 사용하지 않음|TARGET_DOCKER_USERNAME|대상 컨테이너 레지스트리에 로그인하는 데 사용되는 사용자 이름입니다(있는 경우).|
|대상 컨테이너 레지스트리용 암호 입력 - ENTER 키를 누르면 아무것도 사용하지 않음|TARGET_DOCKER_PASSWORD|대상 컨테이너 레지스트리에 로그인하는 데 사용되는 암호입니다(있는 경우). 마스킹된 암호 프롬프트입니다.  입력하거나 붙여넣는 경우에 암호가 보이지 않습니다.|
|대상 이미지용 컨테이너 이미지 태그 입력|TARGET_DOCKER_TAG|일반적으로 혼동을 피하기 위해 원본과 동일한 태그를 사용합니다.|