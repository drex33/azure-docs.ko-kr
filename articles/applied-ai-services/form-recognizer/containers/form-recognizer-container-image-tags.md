---
title: 폼 인식기 이미지 태그 및 릴리스 정보
titleSuffix: Azure Applied AI Services
description: 모든 양식 인식기 컨테이너 이미지 태그의 목록입니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/02/2021
ms.author: lajanuar
keywords: Docker, 컨테이너, 이미지
ms.openlocfilehash: d38fd74324246463e9f74ff8d53c67ccb2ccd039
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123482197"
---
# <a name="form-recognizer-container-image-tags-and-release-notes"></a>양식 인식기 컨테이너 이미지 태그 및 릴리스 정보

> [!IMPORTANT]
>
> * **양식 인식기 v 2.1 컨테이너** 는 제어 된 미리 보기 상태입니다. 이 컨테이너를 사용하려면 [온라인 요청](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)을 제출하고 승인을 받아야 합니다. 
>
> * 온라인 요청 양식을 사용하려면 Azure 구독 ID를 소유하는 조직에 속하고 해당 구독에 대한 액세스 권한이 있거나 액세스 권한이 부여된 유효한 메일 주소를 제공해야 합니다.

## <a name="feature-containers"></a>기능 컨테이너

Form Recognizer 기능은 7개의 컨테이너에서 지원됩니다.

| 컨테이너 이름 | 정규화된 이미지 이름 |
|---|---|
| **레이아웃** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout |
| **명함** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard |
| **ID 문서** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document |
| **Receipt** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt |
| **청구서** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice |
| **사용자 지정 API** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api |
| **사용자 지정 감독** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised |

## <a name="microsoft-container-registry"></a>Microsoft 컨테이너 레지스트리

양식 인식기 컨테이너 이미지는 Microsoft Container Registry **mcr. Microsoft** 에서 찾을 수 있습니다. 모든 Microsoft 게시 Docker 이미지에 대 한 기본 레지스트리 인 **<span></span> com** 컨테이너 레지스트리 게시.

* MCR에 대 한 검색 환경은 [docker 허브](https://hub.docker.com/publishers/microsoftowner)를 통해 제공 됩니다.

* [Mcr 내에서 리포지토리 목록을](https://mcr.microsoft.com/v2/_catalog)쿼리할 수도 있습니다.

## <a name="form-recognizer-tags"></a>폼 인식기 태그

양식 인식기에 사용할 수 있는 태그는 다음과 같습니다.

### <a name="latest-version"></a>[최신 버전](#tab/current)

`v2.1`(제어 된 미리 보기)에 대 한 릴리스 정보:

| 컨테이너 | 태그 |
|------------|:------|
| **레이아웃**| &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.0.016140001-08108749-amd64-preview`|
| **명함** | &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.016190001-amd64-preview`  </br> &bullet; `2.1.016320001-amd64-preview`  |
| **ID 문서** | &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **Receipt**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **청구서**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **사용자 지정 API** | &bullet; `latest` </br> &bullet;`2.1-distroless-20210622013115034-0cc5fcf6`</br>&bullet; `2.1-preview`|
| **사용자 지정 감독**| &bullet; `latest` </br> &bullet; `2.1-distroless-20210622013149174-0cc5fcf6`</br>&bullet; `2.1-preview`|

### <a name="previous-versions"></a>[이전 버전](#tab/previous)

> [!IMPORTANT]
> Form Recognizer v1.0 컨테이너는 사용 중지되었습니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Form Recognizer 컨테이너 설치 및 실행](form-recognizer-container-install-run.md)
>