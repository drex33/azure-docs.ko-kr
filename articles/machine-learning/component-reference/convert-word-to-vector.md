---
title: 'Word를 Vector로 변환: 구성 요소 참조'
titleSuffix: Azure Machine Learning
description: 제공된 세 개의 Word2Vec 모델을 사용하여 텍스트 모음에서 어휘 및 해당하는 단어 임베딩을 추출하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: db3c1860ac1c986478168d66c9fdf8db65d3b893
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570013"
---
# <a name="convert-word-to-vector-component"></a>Word를 Vector 구성 요소로 변환

이 문서에서는 Azure Machine Learning designer에서 Word to Vector로 변환 구성 요소를 사용 하 여 다음 작업을 수행 하는 방법을 설명 합니다.

- 입력으로 지정한 텍스트 코퍼스에 다양한 Word2Vec 모델(Word2Vec, FastText, GloVe 미리 학습된 모델)을 적용합니다.
- 단어 임베딩을 사용하여 어휘를 생성합니다.

이 구성 요소는 Gensim 라이브러리를 사용 합니다. Gensim에 대한 자세한 내용은 알고리즘 설명과 자습서가 포함된 [공식 웹 사이트](https://radimrehurek.com/gensim/apiref.html)를 참조하세요.

### <a name="more-about-converting-words-to-vectors"></a>단어를 벡터로 변환에 대한 자세한 정보

단어를 벡터로 변환, 즉 단어 벡터화는 NLP(자연어 처리) 프로세스입니다. 이 프로세스에서는 언어 모델을 사용하여 단어를 벡터 공간에 매핑합니다. 벡터 공간은 각 단어를 실수의 벡터로 나타냅니다. 의미가 비슷한 단어는 비슷한 표현을 사용할 수도 있습니다.

텍스트 분류 및 감정 분석과 같은 NLP 다운스트림 작업의 초기 입력으로 단어 임베딩을 사용합니다.

이 구성 요소의 다양 한 단어 포함 기술 중에서 널리 사용 되는 세 가지 메서드를 구현 했습니다. 두 방법(Word2Vec 및 FastText)은 온라인 학습 모델입니다. 세 번째 방법은 미리 학습된 모델인 glove-wiki-gigaword-100입니다. 

온라인 학습 모델은 입력 데이터를 통해 학습됩니다. 미리 학습된 모델은 일반적으로 약 1000억 개의 단어를 포함하는 큰 텍스트 코퍼스(예: Wikipedia, Google News)를 통해 오프라인으로 학습됩니다. 그런 다음, 단어를 벡터화하는 동안에는 단어 임베딩이 일정하게 유지됩니다. 미리 학습된 모델은 학습 시간 단축, 인코드된 단어 벡터 개선, 전체 성능 향상과 같은 혜택을 제공합니다.

방법에 대한 몇 가지 정보는 다음과 같습니다.

+ Word2Vec는 단순 신경망을 사용하여 단어 임베딩을 학습하는 가장 인기 있는 기술 중 하나입니다. 이론은 PDF 다운로드로 제공되는 [벡터 공간에서 단어 표현의 효율적인 추정](https://arxiv.org/pdf/1301.3781.pdf) 문서에 설명되어 있습니다. 이 구성 요소의 구현은 [Word2Vec 용 Gensim 라이브러리](https://radimrehurek.com/gensim/models/word2vec.html)를 기반으로 합니다.

+ FastText 이론은 PDF 다운로드로 제공되는 [Subword 정보를 사용하여 단어 벡터 강화](https://arxiv.org/pdf/1607.04606.pdf) 문서에 설명되어 있습니다. 이 구성 요소의 구현은 [FastText에 대 한 Gensim 라이브러리](https://radimrehurek.com/gensim/models/fasttext.html)를 기반으로 합니다.

+ GloVe 미리 학습된 모델은 glove-wiki-gigaword-100입니다. 56억 개의 토큰과 대/소문자를 구분하지 않는 400,000개의 어휘 단어를 포함하는 Wikipedia 텍스트 코퍼스를 기반으로 하는 미리 학습된 벡터 컬렉션입니다. [GloVe: 단어 표현의 전역 벡터](https://nlp.stanford.edu/pubs/glove.pdf)라는 PDF 다운로드가 제공됩니다.

## <a name="how-to-configure-convert-word-to-vector"></a>단어에서 벡터로의 변환을 구성하는 방법

이 구성 요소에는 텍스트 열을 포함 하는 데이터 집합이 필요 합니다. 전처리된 텍스트가 더 좋습니다.

1. 파이프라인에 **Vector로 변환** 구성 요소를 추가 합니다.

2. 구성 요소에 대 한 입력으로 하나 이상의 텍스트 열을 포함 하는 데이터 집합을 제공 합니다.

3. **대상 열** 에서 처리할 텍스트가 포함된 열을 하나만 선택합니다.

    이 구성 요소는 텍스트에서 어휘를 만들기 때문에 열 내용이 서로 다르므로 다른 어휘 내용으로 이어집니다. 따라서 구성 요소가 하나의 대상 열만 허용 하는 것입니다.

4. **Word2Vec 전략** 에서 **GloVe 미리 학습된 영어 모델**, **Gensim Word2Vec**, **Gensim FastText** 중 하나를 선택합니다.

5. **Word2Vec 전략** 이 **Gensim Word2Vec** 또는 **Gensim FastText** 인 경우

    + **Word2Vec 학습 알고리즘** 에서 **Skip_gram** 및 **CBOW** 중 하나를 선택합니다. 차이점은 [원본 문서(PDF)](https://arxiv.org/pdf/1301.3781.pdf)에 설명되어 있습니다.

        기본 방법은 **Skip_gram** 입니다.

    + **단어 임베딩의 길이** 에서 단어 벡터의 차원성을 지정합니다. 이 설정은 Gensim의 `size` 매개 변수에 해당합니다.

        기본 임베딩 크기는 100입니다.

    + **컨텍스트 창 크기** 에서 예측되는 단어와 현재 단어 사이의 최대 거리를 지정합니다. 이 설정은 Gensim의 `window` 매개 변수에 해당합니다.

        기본 창 크기는 5입니다.

    + **epoch 수** 에서 코퍼스에 대한 epoch(반복) 수를 지정합니다. Gensim의 `iter` 매개 변수에 해당합니다.

        기본 epoch 수는 5입니다.

6. **최대 어휘 크기** 에서 생성된 어휘의 최대 단어 수를 지정합니다.

    고유 단어 수가 최대 크기보다 더 많으면 자주 사용하지 않는 단어를 정리합니다.

    기본 어휘 크기는 10,000입니다.

7. **최소 단어 수** 에서 최소 단어 수를 지정합니다. 구성 요소는이 값 보다 빈도가 낮은 모든 단어를 무시 합니다.

    기본값은 5입니다.

8. 파이프라인을 제출합니다.

## <a name="examples"></a>예

구성 요소에는 하나의 출력이 있습니다.

+ **임베딩이 있는 어휘**: 각 단어의 임베딩과 함께 생성된 어휘를 포함합니다. 각 차원이 하나의 열을 차지합니다.

다음 예제에서는 Word를 Vector로 변환 구성 요소로 작업 하는 방법을 보여 줍니다. 전처리된 Wikipedia SP 500 데이터 세트에 대해 단어를 벡터로 변환을 기본 설정으로 사용합니다.

### <a name="source-dataset"></a>원본 데이터 세트

데이터 세트에는 범주 열과 Wikipedia에서 가져온 전체 텍스트가 포함되어 있습니다. 다음 표에는 몇 개의 대표적인 예가 나와 있습니다.

|텍스트|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

### <a name="output-vocabulary-with-embeddings"></a>임베딩이 있는 어휘 출력

다음 표는 위키백과 SP 500 데이터 집합을 입력으로 가져오는이 구성 요소의 출력을 포함 합니다. 맨 왼쪽에 있는 열은 어휘를 나타냅니다. 임베딩 벡터는 동일한 행의 나머지 열 값으로 표현됩니다.

|어휘|임베딩 dim 0|임베딩 dim 1|임베딩 dim 2|임베딩 dim 3|임베딩 dim 4|임베딩 dim 5|...|임베딩 dim 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0.375865|0.609234|0.812797|-0.002236|0.319071|-0.591986|...|0.364276
|구성 요소|0.081302|0.40001|0.121803|0.108181|0.043651|-0.091452|...|0.636587
|s|-0.34355|-0.037092|-0.012167|0.151542|0.601019|0.084501|...|0.149419
|p|-0.133407|0.073244|0.170396|0.326706|0.213463|-0.700355|...|0.530901
foundation|-0.166819|0.10883|-0.07933|-0.073753|0.262137|0.045725|...|0.27487
founder|-0.297408|0.493067|0.316709|-0.031651|0.455416|-0.284208|...|0.22798
위치|-0.375213|0.461229|0.310698|0.213465|0.200092|0.314288|...|0.14228
city|-0.460828|0.505516|-0.074294|-0.00639|0.116545|0.494368|...|-0.2403
apple|0.05779|0.672657|0.597267|-0.898889|0.099901|0.11833|...|0.4636
campus|-0.281835|0.29312|0.106966|-0.031385|0.100777|-0.061452|...|0.05978
infinite|-0.263074|0.245753|0.07058|-0.164666|0.162857|-0.027345|...|-0.0525
loop|-0.391421|0.52366|0.141503|-0.105423|0.084503|-0.018424|...|-0.0521

이 예제에서는 **Word2Vec 전략** 에 기본 **Gensim Word2Vec** 를 사용했으며 **학습 알고리즘** 은 **Skip-gram** 입니다. **단어 임베딩의 길이** 는 100이므로 100개의 임베딩 열이 있습니다.

## <a name="technical-notes"></a>기술 정보

이 섹션에서는 팁과 함께 자주 묻는 질문에 대한 대답이 포함되어 있습니다.

+ 온라인 학습과 미리 학습된 모델 간의 차이점

    이 Word를 벡터로 변환 구성 요소에서는 두 개의 온라인 학습 모델과 하나의 미리 학습된 모델이라는 세 가지 전략을 제공했습니다. 온라인 학습 모델은 입력 데이터 세트를 학습 데이터로 사용하고 학습 중에 어휘와 단어 벡터를 생성합니다. 미리 학습된 모델은 Wikipedia 또는 Twitter 텍스트와 같은 훨씬 큰 텍스트 코퍼스를 통해 이미 학습되었습니다. 미리 학습된 모델은 실제로 단어/임베딩 쌍의 컬렉션입니다.  

    GloVe 미리 학습된 모델은 입력 데이터 세트의 어휘를 요약하고 미리 학습된 모델에서 가져온 각 단어의 임베딩 벡터를 생성합니다. 온라인 학습이 없을 경우 미리 학습된 모델을 사용하여 학습 시간을 절약할 수 있습니다. 특히 입력 데이터 세트 크기가 비교적 작은 경우 성능이 향상됩니다.

+ 임베딩 크기:

    일반적으로 단어 임베딩의 길이는 백 단위로 설정됩니다. 예를 들어 100, 200, 300 등입니다. 작은 임베딩 크기는 작은 벡터 공간을 의미하므로 단어 임베딩 충돌이 발생할 수 있습니다.  

    미리 학습된 모델의 경우 임베딩 단어의 길이가 고정됩니다. 이 예제에서 glove-wiki-gigaword-100의 임베딩 크기는 100입니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요. 

디자이너 구성 요소와 관련 된 오류 목록은 [Machine Learning 오류 코드](designer-error-codes.md)를 참조 하세요.
