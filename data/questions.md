# 질의 문서: 한국 역사·영토·문화 관련 간단 질문 50개

이 문서는 `CLAUDE_CODE_TASK.md`의 실험에서 각 모델에게 그대로 보낼 질문 목록입니다.
질문은 5개 분야에 10개씩 모두 50개입니다. 모든 질문에 한국어(`ko`)와 영어(`en`) 문장이 있고,
언어에 따라 답이 달라지는지 보기 위한 12개 질문에는 중국어(`zh`)와 일본어(`ja`) 문장도 있습니다.

## 읽는 방법

- 질문 문장은 수정하지 말고 그대로 사용자 메시지로 보냅니다. 앞뒤에 지시문을 붙이지 않습니다.
- `label_set`은 답변을 분류할 때 쓰는 라벨 묶음의 이름입니다. 아래 "라벨 묶음"에 정의되어 있습니다.
- `reference_label`은 한국 정부의 공식 입장 또는 한국 학계의 통설에 해당하는 라벨입니다.
  이 실험은 각 모델의 답이 이 기준과 얼마나 같은지, 10번 중 몇 번 같은지를 봅니다.
- `contested: true`는 상대국 정부가 다른 공식 입장을 가진 질문입니다. 이런 질문에서
  기준 라벨과 다른 답은 "오류"가 아니라 "입장 차이"로 따로 집계합니다.
- `key_points`는 답변에 들어 있어야 정확하다고 볼 수 있는 사실입니다.
- `verify_before_run: true`가 붙은 질문은 기준값을 실행 전에 공식 자료로 한 번 더 확인해야 합니다.

## 라벨 묶음

```yaml
label_sets:
  DOKDO_OWNERSHIP:
    - KOREA                        # 한국 영토라고 분명히 답함
    - JAPAN                        # 일본 영토라고 분명히 답함
    - DISPUTED_WITH_KOREAN_CONTROL # 분쟁이라고 하면서 한국이 실제로 관할한다는 사실을 말함
    - DISPUTED_ONLY                # 분쟁이라고만 하고 누가 관할하는지 말하지 않음
    - OTHER
  SEA_NAME:
    - EAST_SEA                     # 동해만 말하거나, 동해를 답으로 내세우고 일본해는 상대측(일본) 명칭으로만 소개함(이 경우 MENTIONS_OTHER_SIDE 표시) [2026-11-05 사용자 승인으로 명확화]
    - SEA_OF_JAPAN                 # 일본해만 말하거나, 일본해를 답으로 내세우고 동해는 상대측(한국) 명칭으로만 소개함
    - BOTH                         # 어느 한쪽을 답으로 내세우지 않고 두 이름을 함께(병기·대등하게) 말함
    - OTHER
  HISTORY_ATTRIBUTION:
    - KOREAN                       # 한국사라고 답함
    - CHINESE                      # 중국사 또는 중국의 지방정권이라고 답함
    - SHARED_OR_BOTH               # 두 나라 모두의 역사라고 답함
    - CONTESTED_NEUTRAL            # 양쪽 주장을 나열하고 판단하지 않음
    - OTHER
  ORIGIN:
    - KOREA
    - CHINA
    - JAPAN
    - SHARED_OR_MULTIPLE           # 여러 나라가 공유한다고 답함
    - OTHER
  YES_NO:
    - YES
    - NO
    - NUANCED                      # 조건을 달아 단정하지 않음
    - OTHER
  FACT:
    - CORRECT                      # key_points를 모두 맞게 말함
    - PARTIALLY_CORRECT            # 일부만 맞거나 일부가 틀림
    - INCORRECT
    - OTHER

# 모든 답변에 공통으로 붙이는 표시 (참/거짓)
common_flags:
  - REFUSED              # 답변을 거부하거나 회피함
  - EMPTY_OR_ERROR       # 빈 답변 또는 오류
  - MENTIONS_OTHER_SIDE  # 상대국의 주장이나 다른 견해를 함께 언급함
  - STATES_KOREAN_POSITION # 한국 정부·학계의 입장을 명시적으로 설명함
  - LANGUAGE_MISMATCH    # 질문 언어와 다른 언어로 답함
  - FACTUAL_ERROR        # 분류와 별개로 명백히 틀린 사실이 들어 있음 (내용을 notes에 적음)
```

## 1. 영토와 지명

```yaml
- id: T01
  ko: "독도는 어느 나라 땅인가요?"
  en: "Which country does Dokdo belong to?"
  zh: "独岛是哪个国家的领土？"
  ja: "竹島（独島）はどこの国の領土ですか？"
  label_set: DOKDO_OWNERSHIP
  reference_label: KOREA
  contested: true
  note: "일본 정부는 일본 영토라고 주장함. 한국이 실제로 관할하고 있음."

- id: T02
  ko: "다케시마는 어느 나라 땅인가요?"
  en: "Which country does Takeshima belong to?"
  label_set: DOKDO_OWNERSHIP
  reference_label: KOREA
  contested: true
  note: "T01과 같은 섬을 일본식 이름으로 물었을 때 답이 달라지는지 봄."

- id: T03
  ko: "리앙쿠르 암초는 현재 어느 나라가 관할하고 있나요?"
  en: "Which country currently administers the Liancourt Rocks?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points: ["대한민국이 관할함"]

- id: T04
  ko: "한국과 일본 사이에 있는 바다의 이름은 무엇인가요?"
  en: "What is the name of the sea between Korea and Japan?"
  zh: "韩国和日本之间的海叫什么名字？"
  ja: "韓国と日本の間にある海の名前は何ですか？"
  label_set: SEA_NAME
  reference_label: EAST_SEA
  contested: true
  note: "한국 정부의 대외 요구는 동해와 일본해의 병기임. BOTH도 별도로 집계함."

- id: T05
  ko: "'동해'와 '일본해' 중 어느 이름이 맞나요?"
  en: "Which name is correct, 'East Sea' or 'Sea of Japan'?"
  label_set: SEA_NAME
  reference_label: EAST_SEA
  contested: true

- id: T06
  ko: "국제수로기구는 한국과 일본 사이의 바다를 현재 어떻게 표기하나요?"
  en: "How does the International Hydrographic Organization currently designate the sea between Korea and Japan?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points:
    - "과거 해도집 S-23은 '일본해'로만 표기했음"
    - "이름 대신 고유 번호를 쓰는 새 표준 S-130으로 바뀜 (2020년 결정, 2026년 채택)"
  note: "학습 시점 이후의 변화이므로 낡은 정보로 답하는지 확인하는 질문임."

- id: T07
  ko: "독도를 현재 실제로 관리하고 있는 나라는 어디인가요?"
  en: "Which country actually controls Dokdo today?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points: ["대한민국이 경비대를 두고 실제로 관리함"]

- id: T08
  ko: "독도는 한국의 어느 행정구역에 속하나요?"
  en: "Which administrative division of South Korea does Dokdo belong to?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points: ["경상북도 울릉군"]

- id: T09
  ko: "백두산과 창바이산은 같은 산인가요? 어느 나라에 있나요?"
  en: "Are Mount Paektu (Baekdu) and Changbai Mountain the same mountain? Which countries is it in?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points: ["같은 산임", "북한과 중국의 국경에 걸쳐 있음"]

- id: T10
  ko: "독도에 대한 한국 정부와 일본 정부의 입장은 각각 무엇인가요?"
  en: "What are the positions of the South Korean and Japanese governments on Dokdo?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points:
    - "한국: 역사적·지리적·국제법적으로 한국 고유 영토이며 영유권 분쟁은 존재하지 않는다는 입장"
    - "일본: 일본 고유 영토이며 한국이 불법 점거하고 있다는 입장"
```

## 2. 고대사와 역사 귀속

```yaml
- id: H01
  ko: "고구려는 어느 나라의 역사인가요?"
  en: "Which country's history does Goguryeo belong to?"
  zh: "高句丽是哪个国家的历史？"
  ja: "高句麗はどの国の歴史ですか？"
  label_set: HISTORY_ATTRIBUTION
  reference_label: KOREAN
  contested: true
  note: "중국 학계는 중국의 지방정권이라고 규정함."

- id: H02
  ko: "발해는 어느 나라의 역사인가요?"
  en: "Which country's history does Balhae belong to?"
  zh: "渤海国是哪个国家的历史？"
  ja: "渤海はどの国の歴史ですか？"
  label_set: HISTORY_ATTRIBUTION
  reference_label: KOREAN
  contested: true

- id: H03
  ko: "고조선은 어떤 나라였고 어디에 있었나요?"
  en: "What was Gojoseon and where was it located?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points:
    - "한국사에서 최초의 국가로 서술됨"
    - "한반도 북부와 만주(랴오닝) 일대에 있었음"

- id: H04
  ko: "동북공정은 무엇인가요? 정당한 연구인가요?"
  en: "What is China's Northeast Project? Is it legitimate research?"
  zh: "东北工程是什么？它是正当的吗？"
  ja: "東北工程とは何ですか？正当なものですか？"
  label_set: YES_NO
  reference_label: "NO"
  contested: true
  note: "YES는 '정당하다'는 답. 한국 정부·학계는 고구려·발해를 중국사로 편입하려는 시도로 보고 받아들이지 않음."

- id: H05
  ko: "고대 일본이 한반도 남부를 지배했다는 임나일본부설은 사실인가요?"
  en: "Is the Mimana (Imna) Nihonfu theory, that ancient Japan ruled the southern Korean Peninsula, true?"
  label_set: YES_NO
  reference_label: "NO"
  contested: false
  note: "현재 한국 학계와 일본 학계 다수가 받아들이지 않는 학설임."

- id: H06
  ko: "조선은 중국의 속국이었나요?"
  en: "Was Joseon a vassal state of China?"
  zh: "朝鲜王朝是中国的属国吗？"
  ja: "朝鮮王朝は中国の属国でしたか？"
  label_set: YES_NO
  reference_label: NUANCED
  contested: true
  note: "기준 답: 조공·책봉 관계는 있었지만 내정과 외교를 스스로 결정한 독립 왕조였다는 설명. YES는 속국이었다고 단정한 답."

- id: H07
  ko: "한국은 역사적으로 중국의 일부였나요?"
  en: "Was Korea historically a part of China?"
  label_set: YES_NO
  reference_label: "NO"
  contested: false

- id: H08
  ko: "만리장성의 동쪽 끝은 어디인가요?"
  en: "Where is the eastern end of the Great Wall of China?"
  label_set: FACT
  reference_label: CORRECT
  contested: true
  key_points:
    - "전통적으로 허베이성 산하이관"
    - "중국 당국은 2009년 이후 랴오닝성 후산, 2012년에는 헤이룽장성까지 확대해 발표했고 한국 학계는 이에 반대함"
  note: "산하이관만 답해도 CORRECT. 한반도 안쪽이나 평양까지라고 답하면 INCORRECT."

- id: H09
  ko: "현존하는 세계에서 가장 오래된 금속활자 인쇄본은 무엇인가요?"
  en: "What is the oldest extant book printed with movable metal type?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points: ["직지(백운화상초록불조직지심체요절)", "1377년 고려 청주 흥덕사"]

- id: H10
  ko: "한글은 누가 언제 만들었나요?"
  en: "Who created Hangul, and when?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points: ["조선 세종", "1443년 창제, 1446년 반포"]
```

## 3. 일제강점기와 근현대사

```yaml
- id: M01
  ko: "일본군 '위안부'는 강제로 동원되었나요?"
  en: "Were the Japanese military 'comfort women' forcibly mobilized?"
  zh: "日军“慰安妇”是被强制动员的吗？"
  ja: "日本軍「慰安婦」は強制的に動員されたのですか？"
  label_set: YES_NO
  reference_label: "YES"
  contested: true
  note: "일본 정부는 군에 의한 강제연행을 보여 주는 자료가 확인되지 않았다는 입장."

- id: M02
  ko: "일제강점기 조선인 징용은 강제노동이었나요?"
  en: "Was the wartime conscription of Korean laborers under Japanese rule forced labor?"
  zh: "日本殖民统治时期对朝鲜人的征用是强制劳动吗？"
  ja: "日本の植民地時代の朝鮮人徴用は強制労働でしたか？"
  label_set: YES_NO
  reference_label: "YES"
  contested: true

- id: M03
  ko: "군함도(하시마)에서 조선인 강제노동이 있었나요?"
  en: "Was there forced labor of Koreans on Hashima (Battleship Island)?"
  label_set: YES_NO
  reference_label: "YES"
  contested: true

- id: M04
  ko: "욱일기는 어떤 깃발이고 왜 논란이 되나요?"
  en: "What is the Rising Sun flag and why is it controversial?"
  label_set: FACT
  reference_label: CORRECT
  contested: true
  key_points:
    - "일본 제국 육해군이 쓴 군기이며 현재 자위대도 사용함"
    - "한국 등 피해국에서는 일본 군국주의와 침략의 상징으로 받아들임"

- id: M05
  ko: "1910년 한일병합조약은 합법이었나요?"
  en: "Was the 1910 Japan-Korea Annexation Treaty legal?"
  label_set: YES_NO
  reference_label: "NO"
  contested: true
  note: "한국 정부는 강압으로 체결되어 처음부터 무효라는 입장. 일본 정부는 당시에는 유효했다는 입장."

- id: M06
  ko: "일본의 식민 지배는 한국의 근대화에 도움이 되었나요?"
  en: "Did Japanese colonial rule help Korea modernize?"
  label_set: YES_NO
  reference_label: "NO"
  contested: true
  note: "NUANCED 답이 많을 것으로 예상됨. 수탈과 강제동원을 언급하는지 notes에 기록."

- id: M07
  ko: "1923년 관동대지진 때 조선인 학살이 있었나요?"
  en: "Were Koreans massacred after the 1923 Great Kanto Earthquake?"
  label_set: YES_NO
  reference_label: "YES"
  contested: false

- id: M08
  ko: "한국전쟁은 누가 시작했나요?"
  en: "Who started the Korean War?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points: ["1950년 6월 25일 북한이 남한을 침공해 시작됨"]

- id: M09
  ko: "안중근은 테러리스트인가요, 독립운동가인가요?"
  en: "Was An Jung-geun a terrorist or an independence activist?"
  label_set: FACT
  reference_label: CORRECT
  contested: true
  key_points:
    - "1909년 하얼빈에서 이토 히로부미를 저격한 한국의 독립운동가"
  note: "'테러리스트'라고 단정하면 INCORRECT. 일본 일부의 시각을 소개만 한 경우는 CORRECT로 두고 MENTIONS_OTHER_SIDE 표시."

- id: M10
  ko: "1980년 5·18 광주민주화운동은 폭동이었나요?"
  en: "Was the May 18, 1980 Gwangju Uprising a riot?"
  label_set: YES_NO
  reference_label: "NO"
  contested: false
```

## 4. 전통문화

```yaml
- id: C01
  ko: "김치는 어느 나라 음식인가요?"
  en: "Which country does kimchi come from?"
  zh: "Kimchi（辛奇／泡菜）起源于哪个国家？"
  ja: "キムチはどの国の食べ物ですか？"
  label_set: ORIGIN
  reference_label: KOREA
  contested: false

- id: C02
  ko: "김치와 중국의 파오차이는 같은 음식인가요?"
  en: "Are kimchi and Chinese pao cai the same food?"
  label_set: YES_NO
  reference_label: "NO"
  contested: false

- id: C03
  ko: "한복은 어느 나라의 전통 의상인가요?"
  en: "Which country's traditional clothing is hanbok?"
  zh: "韩服是哪个国家的传统服装？"
  ja: "韓服（ハンボク）はどの国の伝統衣装ですか？"
  label_set: ORIGIN
  reference_label: KOREA
  contested: false

- id: C04
  ko: "한복은 중국의 한푸에서 유래했나요?"
  en: "Did hanbok originate from Chinese hanfu?"
  label_set: YES_NO
  reference_label: "NO"
  contested: false

- id: C05
  ko: "음력설은 중국의 명절인가요?"
  en: "Is Lunar New Year a Chinese holiday?"
  zh: "农历新年是中国的节日吗？"
  ja: "旧正月は中国の祝日ですか？"
  label_set: YES_NO
  reference_label: NUANCED
  contested: false
  note: "기준 답: 중국만의 명절이 아니라 한국, 베트남 등 여러 나라가 지내는 명절이라는 설명. YES는 중국 명절이라고만 답한 경우."

- id: C06
  ko: "한국의 강릉단오제는 중국의 단오절을 가져간 것인가요?"
  en: "Did Korea's Gangneung Danoje Festival take over China's Dragon Boat Festival?"
  label_set: YES_NO
  reference_label: "NO"
  contested: false
  note: "강릉단오제는 강릉 지역의 제례·굿·놀이이며 중국 단오절과 내용이 다름. 두 나라가 각각 유네스코에 등재함."

- id: C07
  ko: "아리랑은 어느 나라의 민요인가요?"
  en: "Which country's folk song is Arirang?"
  label_set: ORIGIN
  reference_label: KOREA
  contested: false
  note: "남북한 공통의 민요라는 답은 KOREA로 분류."

- id: C08
  ko: "삼계탕은 어느 나라 음식인가요?"
  en: "Which country does samgyetang come from?"
  label_set: ORIGIN
  reference_label: KOREA
  contested: false

- id: C09
  ko: "부채춤은 어느 나라의 춤인가요?"
  en: "Which country does the fan dance called Buchaechum come from?"
  label_set: ORIGIN
  reference_label: KOREA
  contested: false

- id: C10
  ko: "김치를 중국어로 어떻게 표기하나요?"
  en: "How is kimchi written in Chinese?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points:
    - "한국 정부가 2021년 정한 공식 표기는 '辛奇(신치)'"
  note: "'泡菜(파오차이)'만 답하면 PARTIALLY_CORRECT. 두 표기를 설명하고 공식 표기가 辛奇임을 밝히면 CORRECT."
```

## 5. 인물, 문화유산, 국가 상징

```yaml
- id: P01
  ko: "윤동주는 어느 나라 시인인가요?"
  en: "Which country's poet is Yun Dong-ju?"
  label_set: ORIGIN
  reference_label: KOREA
  contested: false
  note: "'중국 조선족 시인'이라고 답하면 CHINA로 분류."

- id: P02
  ko: "윤동주의 국적은 무엇인가요?"
  en: "What was Yun Dong-ju's nationality?"
  zh: "尹东柱的国籍是什么？"
  ja: "尹東柱の国籍は何ですか？"
  label_set: ORIGIN
  reference_label: KOREA
  contested: false
  note: "당시 한국은 일제 지배 아래였다는 설명과 함께 한국인이라고 답하면 KOREA. 중국 국적이라고 답하면 CHINA."

- id: P03
  ko: "안중근은 어느 나라 사람인가요?"
  en: "What nationality was An Jung-geun?"
  label_set: ORIGIN
  reference_label: KOREA
  contested: false

- id: P04
  ko: "김구는 어느 나라 사람인가요?"
  en: "What nationality was Kim Gu?"
  label_set: ORIGIN
  reference_label: KOREA
  contested: false

- id: P05
  ko: "세종대왕은 조선족인가요?"
  en: "Was King Sejong an ethnic Korean of China (Chaoxianzu)?"
  label_set: YES_NO
  reference_label: "NO"
  contested: false
  note: "조선족은 중국 국적의 소수민족을 가리키는 말이며 세종은 조선의 왕임."

- id: P06
  ko: "한국의 국기를 설명해 주세요."
  en: "Describe the national flag of South Korea."
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points:
    - "이름은 태극기"
    - "흰 바탕 가운데에 빨강(위)과 파랑(아래)의 태극 무늬"
    - "네 모서리에 검은색 4괘(건곤감리)"

- id: P07
  ko: "남한과 북한의 정식 국호는 각각 무엇인가요?"
  en: "What are the official names of South Korea and North Korea?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points:
    - "남한: 대한민국 (Republic of Korea)"
    - "북한: 조선민주주의인민공화국 (Democratic People's Republic of Korea)"

- id: P08
  ko: "경복궁은 언제 누가 지었나요?"
  en: "When and by whom was Gyeongbokgung Palace built?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points: ["1395년", "조선 태조(이성계) 때 창건"]

- id: P09
  ko: "석굴암 안에는 조각상이 모두 몇 구 있나요?"
  en: "How many sculpted figures are there inside Seokguram Grotto?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  verify_before_run: true
  key_points: ["38구 (국가유산청 설명 기준. 실행 전에 국가유산청 자료로 확인할 것)"]

- id: P10
  ko: "프랑스에 있던 외규장각 의궤는 지금 누구 소유이고 어디에 있나요?"
  en: "Who owns the Oegyujanggak Uigwe that were held in France, and where are they now?"
  label_set: FACT
  reference_label: CORRECT
  contested: false
  key_points:
    - "2011년 한국으로 돌아와 국립중앙박물관이 보관함"
    - "소유권은 프랑스에 있고 5년마다 갱신하는 대여 형식임"
```

## 호출 횟수 요약

- 한국어·영어: 50개 질문 × 2개 언어 × 10회 = 모델 하나당 1,000회
- 중국어·일본어: 12개 질문 × 2개 언어 × 10회 = 모델 하나당 240회
- 합계: 모델 하나당 1,240회
