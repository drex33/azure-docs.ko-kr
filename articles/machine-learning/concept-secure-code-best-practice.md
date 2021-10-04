---
title: 보안 코드 모범 사례
titleSuffix: Azure Machine Learning
description: Azure Machine Learning, 완화 및 모범 사례를 위해 개발할 때 있을 수 있는 잠재적인 보안 위협에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: db0696afe7a3b6ba0f28abd4c68f677e90e894b5
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430025"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Azure Machine Learning 관련 보안 코드 모범 사례

Azure Machine Learning에서 모든 원본의 파일 및 콘텐츠를 Azure로 업로드할 수 있습니다. 로드하는 Jupyter Notebook 또는 스크립트 내의 콘텐츠는 잠재적으로 세션에서 데이터를 읽고, Azure에서 조직 내 데이터에 액세스하거나, 사용자를 대신해 악의적인 프로세스를 실행할 수 있습니다.

> [!IMPORTANT]
> 신뢰할 수 있는 원본에서만 Notebook 또는 스크립트를 실행합니다. 사용자 또는 보안 팀에서 Notebook 또는 스크립트를 검토한 위치를 예로 들 수 있습니다.

## <a name="potential-threats"></a>잠재적 위협

Azure Machine Learning를 사용하는 개발에는 웹 기반 개발 환경(Notebook 및 Azure ML 스튜디오)이 포함됩니다. 웹 기반 개발 환경을 사용하는 경우 잠재적인 위협은 다음과 같습니다.

* [XSS(교차 사이트 스크립팅)](https://owasp.org/www-community/attacks/xss/)

    * __DOM 주입__:이 유형의 공격은 브라우저에 표시되는 UI를 수정할 수 있습니다. 예를 들어 Jupyter Notebook에서 실행 단추가 동작하는 방식을 변경합니다.
    * __액세스 토큰/쿠키__: XSS 공격도 로컬 스토리지 및 브라우저 쿠키에 액세스할 수 있습니다. AAD(Azure Active Directory) 인증 토큰은 로컬 스토리지에 저장됩니다. XSS 공격은 이 토큰을 사용하여 사용자 대신 API 호출을 수행한 다음 외부 시스템 또는 API로 데이터를 보낼 수 있습니다.

* [CSRF(교차 사이트 요청 위조)](https://owasp.org/www-community/attacks/csrf): 이 공격은 이미지 또는 링크의 URL을 악성 스크립트나 API의 URL로 바꿀 수 있습니다. 이미지를 로드하거나 링크를 클릭하면 URL에 대한 호출이 수행됩니다.

## <a name="azure-ml-studio-notebooks"></a>Azure Data Studio Notebook

Azure Machine Learning 스튜디오는 브라우저에서 호스트된 Notebook 환경을 제공합니다. Notebook의 셀은 악성 코드를 포함하는 HTML 문서 또는 조각을 출력할 수 있습니다.  출력이 렌더링되면 코드를 실행할 수 있습니다.

__가능한 위협__:
* XSS(교차 사이트 스크립팅)
* CSRF(교차 사이트 요청 위조)

__Azure Machine Learning에서 제공하는 완화__:
* __코드 셀 출력__ 은 iframe에서 샌드박스가 적용됩니다. iframe은 스크립트가 부모 DOM, 쿠키 또는 세션 스토리지에 액세스할 수 없도록 합니다.
* __Markdown 셀__ 콘텐츠는 dompurify 라이브러리를 사용하여 정리됩니다. 이렇게 하면 markdown 셀에서 악성 스크립트가 실행되지 않도록 차단됩니다.
* __이미지 URL__ 및 __Markdown 링크__ 는 악성 값을 확인하는 Microsoft 소유의 엔드포인트로 전송됩니다. 악의적인 값이 검색되면 엔드포인트가 요청을 거부합니다.

__권장 작업__:
* 스튜디오로 업로드하기 전에 파일의 콘텐츠를 신뢰하는지 확인합니다. 업로드할 때 신뢰할 수 있는 파일을 업로드하고 있음을 확인해야 합니다.
* 외부 애플리케이션을 여는 링크를 선택하면 애플리케이션을 신뢰하는지 묻는 메시지가 표시됩니다.

## <a name="azure-ml-compute-instance"></a>Azure ML 컴퓨팅 인스턴스

Azure Machine Learning 컴퓨팅 인스턴스는 __Jupyter__ 및 __Jupyter Lab__ 을 호스트합니다. 둘 중 하나를 사용하는 경우 Notebook의 셀 또는 코드는 악성 코드가 포함된 HTML 문서나 조각을 출력할 수 있습니다. 출력이 렌더링되면 코드를 실행할 수 있습니다. 컴퓨팅 인스턴스에서 호스트되는 __Rstudio__ 를 사용하는 경우에도 동일한 위협이 적용됩니다.

__가능한 위협__:
* XSS(교차 사이트 스크립팅)
* CSRF(교차 사이트 요청 위조)

__Azure Machine Learning에서 제공하는 완화__:
* 없음 Jupyter 및 Jupyter Lab은 Azure Machine Learning 컴퓨팅 인스턴스에 호스트되는 오픈 소스 애플리케이션입니다.

__권장 작업__:
* 스튜디오로 업로드하기 전에 파일의 콘텐츠를 신뢰하는지 확인합니다. 업로드할 때 신뢰할 수 있는 파일을 업로드하고 있음을 확인해야 합니다.

## <a name="report-security-issues-or-concerns"></a>보안 문제 보고 

Azure Machine Learning은 Microsoft Azure 버그 장려금 프로그램 대상입니다. 자세한 내용은  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning에 대한 엔터프라이즈 보안](concept-enterprise-security.md)