---
title: 개발 도구
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine에서 사용할 수 있는 도구 및 통합 개발 환경에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: efe57637347b5886827f5da443acd9240974d687
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070969"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine의 개발 도구

DSVM(Data Science Virtual Machine)은 생산성이 뛰어난 IDE(통합 개발 환경)에서 널리 사용되는 여러 도구를 번들로 묶습니다. DSVM에서 제공되는 몇 가지 도구는 다음과 같습니다.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

| 범주 | 값 |
|--|--|
| 무엇인가요? | 범용 IDE |
| 지원되는 DSVM 버전 | Windows Server 2019: Visual Studio 2019 |
| 일반적인 사용 용도 | 소프트웨어 개발 |
| DSVM에서 구성 및 설치 방법 | 데이터 과학 워크로드(Python 및 R 도구), Azure 워크로드(Hadoop, Data Lake), Node.js, SQL Server 도구, [Azure Machine Learning for Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai) |
| 사용 및 실행 방법 | 바탕화면 바로 가기(`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). 바탕 화면 아이콘 또는 **시작** 메뉴를 사용하여 그래픽 방식으로 Visual Studio를 엽니다. 프로그램을 검색(Windows 로고 키+S)한 다음, **Visual Studio** 를 찾습니다. 여기서는 C#, Python, R, Node.js와 같은 언어로 프로젝트를 만들 수 있습니다. |

> [!NOTE]
> 평가 기간이 만료되었다는 메시지가 표시될 수 있습니다. Microsoft 계정 자격 증명을 입력합니다. 또는 새 체험 계정을 만들어 Visual Studio Community에 액세스할 수 있습니다.

## <a name="visual-studio-code"></a>Visual Studio Code 

| 범주 | 값 |
|--|--|
| 무엇인가요? | 범용 IDE |
| 지원되는 DSVM 버전 | Windows, Linux |
| 일반적인 사용 용도 | 코드 편집기 및 Git 통합 |
| 사용 및 실행 방법 | Windows에서 바탕화면 바로 가기(`C:\Program Files (x86)\Microsoft VS Code\Code.exe`), Linux에서 바탕화면 바로 가기 또는 터미널(`code`) |

## <a name="rstudio-desktop"></a>RStudio Desktop

| 범주 | 값 |
|--|--|
| 무엇인가요? | R 언어용 클라이언트 IDE |
| 지원되는 DSVM 버전 | Windows, Linux |
| 일반적인 사용 용도 | R 개발 |
| 사용 및 실행 방법 | Windows에서 바탕화면 바로 가기(`C:\Program Files\RStudio\bin\rstudio.exe`), Linux에서 바탕화면 바로 가기(`/usr/bin/rstudio`) |

## <a name="pycharm"></a>PyCharm

| 범주 | 값 |
|--|--|
| 무엇인가요? | Python 언어용 클라이언트 IDE |
| 지원되는 DSVM 버전 | Windows 2019, Ubuntu 18.04 |
| 일반적인 사용 용도 | Python 개발 |
| 사용 및 실행 방법 | Windows에서 바탕화면 바로 가기(`C:\Program Files\tk`), Linux에서 바탕화면 바로 가기(`/usr/bin/pycharm`) |
