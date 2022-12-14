# 딥러닝을 이용한  한글 노래 가사 기반 제목 추천<br>(Song_Title_Recommendation_Model)
<br>

## 0. 목차
<table>
    <thead>
        <tr align=center>
            <th>번호</th>
            <th>목차</th>   
        </tr>
    </thead>
    <tbody>
        <tr align=center>
            <td>1</td>
            <td><a href="#1">주제 선정</a></td>
        </tr>
        <tr align=center>
            <td>2</td>
            <td><a href="#2">데이터 수집</a></td>
        </tr>
        <tr align=center>
            <td>3</td>
            <td><a href="#3">데이터 전처리</a></td>
        </tr>
        <tr align=center>
            <td>4</td>
            <td><a href="#4">모델 선정</a></td>
        </tr>
        <tr align=center>
            <td>5</td>
            <td><a href="#5">모델 성능 평가</a></td>
        </tr>
        <tr align=center>
            <td>6</td>
            <td><a href="#6">결과</a></td>
        </tr>
        <tr align=center>
            <td>7</td>
            <td><a href="#7">자체 평가 의견</a></td>
        </tr>
        <tr align=center>
            <td>8</td>
            <td><a href="#8">사용 코드</a></td>
        </tr>
        <tr align=center>
            <td>9</td>
            <td><a href="#9">멤버 & 역할</a></td>
        </tr>  
     </tbody>
</table>
<br>
       

## 1.<a name="1">주제 선정</a>
### ○ 프로젝트 주제 
- 딥러닝을 이용한 한글 노래 가사 기반 제목 추천 모델 구현<br> 
<br>

### ○ 프로젝트 기획의도 
- 노래 제목이 가수의 심리상태와 흥행에 영향을 미친다는 속설이 있고, 그만큼 가수는 제목 선정에 신경을 많이 쓰며 어려움을 겪고 있다.
<div><img width=750 height=300px src="https://user-images.githubusercontent.com/79880476/203907006-9d8ab5fe-c8d2-4fe1-9c67-1e1397728cad.jpg"></div>
<br>

### ○ 프로젝트 기간
 &nbsp;  ●  2022. 9. 13. ~ 2022. 9. 20. 
<br>
<br>

### ○ 프로젝트 목적
- 논문이나 발표PPT의 제목 선정에 고민과 어려움을 안고 있기 때문에 딥러닝을 이용해 제목을 생성요약을 시도.
- LSTM, Transformer를 이용한 모델 핸들링과 pretrained 된 모델을 파인튜닝 등의 모델 공부.
<br>

### ○ 프로젝트 기대효과
- 딥러닝을 통해 유사하거나 괜찮은 제목을 뽑아주면 앞으로도 활용가능한 부분이 많다.
<br>

## 2.<a name="2">데이터 수집</a>
### ○ 데이터셋
- 500,000곡 크롤링 - 멜론, 벅스 뮤직 등에서 50,000곡 가사 크롤링 & 음악 블로그 한국 60,000만곡, 해외 40,000만곡 크롤링+ 벅스 뮤직에서 350,000곡 추가 크롤링
<table>
   <tr>
      <td colspan=2><img width=750 height=300px src="https://user-images.githubusercontent.com/79880476/203907000-d116a0aa-f83f-4366-a515-1d65801cd194.jpg"></td>
    </tr>
    <tr align=center>
      <td width="50%"> 가사가 등록되어 있는 음악 페이지(멜론,벅스)</td>
      <td width="50%"> 크롤링 해온 가사 데이터의 데이터프레임 </td>           
    </tr>
</table>
<br>

## 3.<a name="3">데이터 전처리</a>
### ○ 크롤링한 기사 데이터 전처리
- 1.중복되는 곡들의 제목과 가사를 제거

        ○ drop_duplicates() 함수를 이용하여 중복제거
        
        lylic.drop_duplicates(subset=None, keep='first', inplace=False, ignore_index=False)
        
- 2.가사의 끝부분에 필요없는 문장 제거

        ○ replace() 함수를 이용하여 치환
        
            lylic[i] = lylic[i].replace('Bugs 님이 등록해 주신 가사입니다.', '')
        
            "\n\nBugs 님이 등록해 주신 가사입니다.\r\n\t\t\t\t\t\t\n가사 오류 제보\n" → " "
        
        
- 3.제목과 가사를 한글만 남기고 지우고, 추가로 다수의 공백을 하나로 치환

        ○ 정규표현식 re.sub() 함수를 이용하여 치환
        
            title[i] = re.sub('\([^)]+\)', '',str(title[i])) #가로안에 가로는 못 없앰
            title[i] = re.sub('\([^*]+\)', '',str(title[i])) #가로안에 내용 다 없앰
            title[i] = re.sub('[-=+,#/\?:^$.@*\"※~&%ㆍ!』\\‘|\(\)\[\]\<\>`\'…》]', '',str(title[i]))# 특수문자 삭제

- 4.형태소 분석기 mecab 사용
 
        ○ 정규표현식 re.sub() 함수를 이용하여 치환
        
            from konlpy.tag import Mecab
            mecab = Mecab()
                ex) ['천만', '분', '의', '의', '확률', '의', '너']
           
- 5.전처리 과정에서 영어ㆍ숫자 제거로 조사 반복 발생 → 부자연스러운 조사 제거

             ex) ['천만', '분', '의', '의', '확률', '의', '너'] → ['천만', '분', '의', '확률', '의', '너']


- 6.제목과 가사의 토큰 개수가 1개 이하인것 제거

             ex) ['흔적'] ['마침표'] ['후회'] -> []

- 7.가사의 토큰수가 지나치게 적거나 많은 노래 제거

             ex) 토큰 40개 이하 제거, 토큰 800개 이상 제거

<br>

## 4.<a name="4">모델 선정</a>
#### ○ LSTM, Transformer를 직접 학습하여 모델핸들링 , Pretrained된 모델 KoBERT, KoBART, KoGPT2를 파인튜닝 한 결과 <br> → ∴KoGPT2가 제일 성능이 좋았다.
<table>
   <tr>
      <td><img width=750 height=300px src="https://user-images.githubusercontent.com/79880476/203922843-00fd86f2-8c1f-4ec4-8a37-743ace5bf95d.jpg"></td>
      <td><img width=750 height=300px src="https://user-images.githubusercontent.com/79880476/203922837-ce08e1ba-4e59-4b93-b0f4-9767b1602c7b.jpg"></td>
    </tr>
    <tr align=center>
      <td width="50%">KoGPT2의 모델 구조도</td>
      <td width="50%">학습시킨 데이터 & 파인튜닝한 데이터의 구조</td>           
    </tr>
</table>
<br>
       
## 5.<a name="5">모델 성능 평가</a>
### ○ KoGPT2에 Fine-tuning한 결과
<img width=900 height=200px src="https://user-images.githubusercontent.com/79880476/203924393-caa15d74-43e3-4f67-a699-96654888a627.jpg">

- 국내 가요는 사랑에 대한 주제 多 때문에 편향 문제 발생되었다고 판단함.<br>
- 한국어만 입력한 가사에 영어 제목이 추천되는 문제 식별되었다.<br>
 &nbsp; &nbsp;→ 추가적인 데이터 추가와 데이터 전처리를 해줘야된다고 판단함.
<br>

## 6.<a name="6">결과</a>
### ○ 데이터 350,000곡 가사추가 & 데이터 전처리 과정을 통해 데이터 정제한 데이터로 Fine-tuning한 결과
<img width=900 height=200px src="https://user-images.githubusercontent.com/79880476/203924402-11ca73b3-7115-4de2-b6ed-a3cfacfee0b7.jpg">

- 가사 내용과 예측 제목의 연관성 상승을 통해 자연스러운 예측 제목 빈도가 올라갔다.<br>
- 영어를 제거하는 전처리로 한국어로만 이루어진 추천 제목 나온다.<br>
- 생성된 제목에 ‘사랑’이라는 단어가 들어가는 편향 문제 일부 개선하였다.<br>
<br>

## 7.<a name="7">자체 평가 의견</a>
### ○ 한계점
- 만족스러운 정답을 얻기 위해 1~10차례 예측 시도 요구된다.
- 한국의 정서상 사랑에 대한 내용 많기 때문에 추후 다양한 주제의 균형된 데이터 추가 학습 필요하다.
- 요즘 한국 노래에 영어 가사 상당수 포함되어있지만, 전처리로 영어 제외하여 제외된 영어 토큰 앞뒤의 문맥 정보 일부 훼손되었다. 추후 다국어 학습 모델 사용 또는 영어 포함 전처리 방안 모색이 필요하다.<br>
### ○ 결론
- KoGPT2 Model을 이용해 한글 가사로 노래 제목을 추천해주는 프로그램 구현하였다.
- 데이터 추가 및 학습이 진행될수록 예측된 제목과 가사의 연관성이 높아지는 것을 식별되었다.
- 추후 데이터 추가 및 학습을 더욱 진행시키고, 효율적인 데이터 전처리를 진행한다면 상용화가 될 것이다.
<br>

## 번외. 추천받은 제목으로 노래 가사 생성
<table>
   <tr>
      <td><img width=900 height=300px src="https://user-images.githubusercontent.com/79880476/203928601-5da80ded-3f7c-4384-869f-a27da056e7ae.jpg"></td>
    </tr>
    <tr align=center>
      <td>'리쌍-광대'의 기사를 통해 제목'어른의 노래'가 나왔고(텍스트요약), 이를 통해 가사 생성(텍스트생성)을 시켜봤다.</td>         
    </tr>
</table>
<br>

## 8.<a name="8">사용 코드</a>
<table>
    <thead>
        <tr>
            <th>목록</th>
            <th>파일명</th>
            <th>설명</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>데이터 크롤링 파일</td>
            <td><a href = "https://github.com/dydwo322/Song_title_recommendation/blob/main/Song_Title_Recommendation_Crawling.ipynb">Song_Title_Recommendation_Crawling.ipynb</a></td>
            <td>벅스,멜론 등 가사를 크롤링하는 코드입니다.</td>
        </tr>
        <tr>
            <td>데이터 전처리 파일</td>
            <td><a href = "https://github.com/dydwo322/Song_title_recommendation/blob/main/Song_Title_Recommendation_Preprocessing.ipynb">Song_Title_Recommendation_Preprocessing.ipynb</a></td>
            <td>크롤링 해온 데이터를 전처리하는 코드입니다.</td>
        </tr>
        <tr>
            <td rowspan ="3">사용 모델 파일</td>
            <td><a href = "https://github.com/dydwo322/Song_title_recommendation/blob/main/Song_Title_Recommendation_Transformer.ipynb">Song_Title_Recommendation_Transformer.ipynb</a></td>
            <td>사용 모델 Transformer의 코드입니다.</td>
        </tr>
        <tr>
            <td><a href = "https://github.com/dydwo322/Song_title_recommendation/blob/main/Song_Title_Recommendation_Fine_tuning_KoBART.ipynb">Song_Title_Recommendation_Fine_tuning_KoBART.ipynb</a></td>
            <td>Fine-tuning에 사용한 모델 KoBART의 코드입니다.</td>
        </tr>
        <tr>
            <td><a href = "https://github.com/dydwo322/Song_title_recommendation/blob/main/Song_Title_Recommendation_Fine_tuning_KoGPT2.ipynb">Song_Title_Recommendation_Fine_tuning_KoGPT2.ipynb</a></td>
            <td>Fine-tuning에 사용한 모델 KoGPT2의 코드입니다.</td>
        </tr>
    </tbody>
</table>
<br>
<br>

## 9.<a name="9">멤버 & 역할</a>
<img width=300 height=300px src="https://user-images.githubusercontent.com/79880476/203930539-4c441ba5-d2f6-493b-b781-235b68efd7f2.jpg">

- 김용재(조장) : 데이터 크롤링(블로그), 데이터 전처리, 모델핸들링(Transformer)
- 국승용 : 데이터 크롤링(블로그), 모델핸들링(KoBART), PPT
- 황성연 : 데이터 크롤링(벅스), 모델핸들링(KoBERT), 데이터 전처리
- 박혜정 : 데이터 크롤링(멜론), 모델핸들링(LSTM Attention), 데이터 전처리, Notion정리
- 한민재 : 모델핸들링(KoGPT2), 코드정리

