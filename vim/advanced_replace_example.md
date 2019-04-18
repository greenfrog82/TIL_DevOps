# Advanced Replace Example

## Replace all character to single space without number 

>s/[^\d]+/ /g

다음과 같이 문자, 숫자, 탭 그리고 스페이스가 섞여있는 문자열이있다. 

>24025	abcd24038	test23951	한국24027	미국 중국24101 melong

위 문자열을 다음과 같은 형태로 변경한다. 

>24025 24038 23951 24027 24101 
