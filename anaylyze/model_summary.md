## 데알못의 데이터분석 서당개 풍월읊기

같이 기술 공부하는 팀에서 요즘 데이터 분석 공부를 하고 있다. 가끔 슬랙에서 나누는 얘기만 슬쩍 봐도 무슨 얘기를 하는지 도통 모르겠다. 앞으로 프로젝트를 계속 이어나가며 개발을 하더라도 분석의 메커니즘을 이해하는게 커뮤니케이션에 도움이 될 것 같아서 데이터분석 스터디를 참관하기로 했다. 오늘 얘기한 분석모델은 어떤 것 인지 간단하게 요약하려고 한다.



### Language Model

text classification과 달리 비지도 학습이다. 주로 highly-rate scoring을 통해 generating 시킨다.

### Autoregressive language modeling (자기회귀 언어 모델)

'실패는 성공의 어머니'라는 문장에서 각 음절을 나누어 '실패는','성공의','어머니' 토큰을 만든다. 그리고 '실패는'이 나올 확률, '실패는' + '성공의' 가 나올 확률, '실패는'+ '성공의' + '어머니' 가 나올 확률값을 계산한다. 팀원 말로는 input/output이 명확해서 지도학습이라고 하는데 내가 잘 이해했는지 모르겠다. 



### N-Grum language model

1. 예측하려면 한번이라도 학습데이터에 들어가야한다.
2. long-term dependency. 나온지 오래된 단어는 감지하기 힘들다.