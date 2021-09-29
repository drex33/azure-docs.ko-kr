---
title: Azure Purview의 인사이트 이해
description: 이 문서에서는 Azure Purview에서 제공하는 인사이트를 설명합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: ff50729dee893caa7a1c38f57cbd23f0470fc84b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218853"
---
# <a name="understand-insights-in-azure-purview"></a>Azure Purview의 인사이트 이해

이 문서에서는 Azure Purview의 인사이트 기능에 대한 개요를 제공합니다.

인사이트는 Purview의 핵심 요소 중 하나입니다. 이 기능은 고객에게 카탈로그에 대한 단일 창 보기를 제공하고 데이터 원본 관리자, 비즈니스 사용자, 데이터 관리자, 데이터 책임자 및 보안 관리자에게 특정 인사이트를 제공하는 것을 목표로 합니다. 현재 Purview에는 Insight의 공개 미리 보기 중에 고객이 사용할 수 있는 다음과 같은 Insights 보고서가 있습니다.

> [!IMPORTANT]
> Azure Purview Insights 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

## <a name="asset-insights"></a>자산 인사이트

이 보고서는 데이터 자산에 대한 조감도와 원본 유형별, 분류별, 파일 크기별 분포를 일부 차원으로 제공합니다. 이 보고서는 Purview 계정을 관리하고 검사를 실행할 수 있는 다양한 유형의 사용자 또는 조직의 데이터 자산 내에서 특정 분류를 사용하여 얼마나 많은 자산이 존재하는지 알고 싶어 하는 비즈니스 사용자를 대상으로 합니다. 

이 보고서는 그래프 및 KPI를 통해 광범위한 인사이트를 제공하고 나중에 잘못된 파일과 같은 특정 변칙을 심층 분석합니다. 또한 이 보고서는 엔드투엔드 사용자 환경을 지원합니다. 여기서 고객은 특정 분류를 사용하여 자산 수를 볼 수 있고, 원본 유형 및 상위 폴더별로 정보를 분석할 수 있으며, 추가 조사를 위해 자산 목록을 볼 수도 있습니다.

## <a name="scan-insights"></a>인사이트 검사

이 보고서를 통해 관리자는 검색의 전반적인 상태(성공한 횟수, 실패한 횟수, 취소된 횟수)를 파악할 수 있습니다. 이 보고서는 지난 7일 또는 지난 30일 동안 Purview 계정에서 실행된 검사에 대한 상태 업데이트를 제공합니다.

또한 관리자는 이 보고서를 통해 실패한 검사와 특정 원본 유형을 심층 분석하고 살펴볼 수 있습니다. 사용자가 조사할 수 있도록 보고서를 통해 “원본” 환경 내에서 검색 기록 페이지로 이동할 수 있습니다.

## <a name="glossary-insights"></a>용어집 인사이트

이 보고서는 비즈니스 사용자 및 데이터 관리자에게 용어집에 대한 상태 보고서를 제공합니다. 사용자는 이 보고서를 통해 상태별 용어집 용어 분포를 파악하고 자산에 첨부된 용어집 용어 수 및 아직 자산에 연결되지 않은 용어집 용어 수를 알아볼 수 있습니다. 비즈니스 사용자는 용어집 용어의 완전성에 대해서도 알아볼 수 있습니다. 

이 보고서에는 비즈니스 사용자 또는 데이터 관리자가 조직에서 사용할 수 있는 완전한 용어집을 만들기 위해 집중해야 하는 주요 항목이 요약되어 있습니다. 사용자는 “용어집 인사이트” 환경에서 “용어집” 환경으로 이동하여 특정 용어집 용어를 변경할 수도 있습니다.

## <a name="classification-insights"></a>분류 인사이트

이 보고서는 분류된 데이터의 위치, 검사 중에 발견된 분류 및 분류된 파일 자체에 대한 드릴다운에 대한 세부 정보를 제공합니다. 이를 통해 보안 관리자는 조직의 데이터 자산에 있는 정보의 유형을 이해할 수 있습니다. 

Azure Purview에서 분류는 subject 태그와 비슷하며 데이터 자산에서 특정 유형의 콘텐츠를 표시하고 식별하는 데 사용됩니다.

특정 분류로 콘텐츠를 식별하고 리포지토리에 보안을 추가하거나 콘텐츠를 보다 안전한 위치로 이동하는 등 필요한 작업을 파악하려면 분류 인사이트 보고서를 사용합니다.

자세한 내용은 [Azure Purview의 데이터에 대한 분류 인사이트](classification-insights.md)를 참조하세요.

## <a name="sensitivity-labeling-insights"></a>민감도 레이블 지정 인사이트

이 보고서는 검사 중에 발견된 민감도 레이블에 대한 세부 정보 및 레이블이 있는 파일 자체에 대한 드릴다운을 제공합니다. 이를 통해 보안 관리자는 조직의 데이터 자산에 있는 정보의 보안을 보장할 수 있습니다. 

Azure Purview에서 민감도 레이블은 각 범주에 적용하려는 그룹 보안 정책뿐 아니라 분류 유형 범주를 식별하는 데 사용됩니다.

콘텐츠에 있는 민감도 레이블을 식별하고 특정 리포지토리 또는 파일에 대한 액세스 관리와 같은 필요한 작업을 파악하려면 레이블 지정 인사이트 보고서를 사용합니다.

자세한 내용은 [Azure Purview의 데이터에 대한 민감도 레이블 인사이트](sensitivity-insights.md)를 참조하세요.

## <a name="file-extension-insights"></a>파일 확장명 인사이트

이 보고서는 검색 중에 발견된 파일 확장명 또는 파일 형식에 대한 세부 정보 및 파일 자체에 대한 드릴다운을 제공합니다. 

파일의 수, 해당 파일이 있는 위치 및 중요한 데이터를 검색할 수 있는지 여부를 파악하려면 파일 확장명 인사이트 보고서를 사용합니다.

자세한 내용은 [Azure Purview의 데이터에 대한 파일 확장명 인사이트](file-extension-insights.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [용어집 인사이트](glossary-insights.md)
* [인사이트 검사](scan-insights.md)
* [분류 인사이트](./classification-insights.md)
