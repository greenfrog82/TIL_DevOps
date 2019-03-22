# [Apdex(Application Performance Index)](http://apdex.org/overview.html)

본 문서는 [Apdex(Application Performance Index)](http://apdex.org/overview.html)을 번역한 것이다. 

`Apdex(Application Performance Index)`는 어플리케이션의 성능을 추적, 벤치마트하고 리포트하기 위한 방법을 정의하는 회사들의 모임에 의해 개발된 공개 표준이다. 

## The Problem

기업들은 자신의 서비스가 좋은 성능을 내도록 노력하고 있다. 하지만 비즈니스적인 관점에서 자신들의 서비스가 좋은 성능을 내고 있는지에 대한 통찰력을 가지고 있지 않다. **응답시간(Response time)**은 사용자들이 얼마나 생산적인지를 드러내지 못하고 샘플들의 가장 큰 값은 혼란스럽도록 한다. **편균화(Averaging)**는 느린 응답에 대해서 사용자들의 불만을 감춰버린다. 시간으로 측정되는 값들은 다른 어플리케이션들 마다 같은 의미를 갖지 않는다. 때문에 **성능에 대해서 무엇이 문제인지 분석하고 측정할 수 있는 더 좋은 방법이 필요하다**.

## The Solution

`Apdex`는 사용자가 기업의 서비스 성능에 대해서 느끼는 만족감을 숫자로 측정한것이다.