# Forguncy AI 모델 비교 샘플 (Claude vs GPT vs Gemini)

Forguncy V12의 **AI 도우미 명령**으로 만든 AI 모델 비교 샘플 앱입니다.
같은 고객 문의 20건을 Claude, GPT, Gemini로 분류하고 **정확도**와 **응답 속도**를 한 화면에서 비교합니다.

> A Forguncy V12 sample app that compares Claude, GPT, and Gemini on the same task. It classifies 20 customer inquiries with each model and shows accuracy and response time side by side, built with no code.

🔗 **데모**: https://demo.forguncy.co.kr/ai_model_test


---

## 주요 기능

- 세 AI 모델로 고객 문의(VOC) 20건을 한 번에 분류
- 모델별 정확도, 정답 수, 전체 처리 시간, 건당 평균 응답 시간 표시
- 문의별로 세 모델의 분류 결과와 응답 시간(ms)을 한 줄에 비교
- 정답과 다른 결과는 빨간색으로 표시

## 실행 환경

| 항목 | 내용 |
| --- | --- |
| Forguncy | V12 이상 (12.0.11.0에서 테스트) |
| AI 모델 | Anthropic, OpenAI, Google Gemini API 키 |

> ⚠️ API 키는 포함되어 있지 않습니다. 각 AI 회사에서 직접 발급받아 등록해 주세요. ChatGPT Plus 같은 구독 계정으로는 사용할 수 없고, 사용량만큼 과금되는 API 키가 필요합니다.

## 파일 구성

```
├── AI모델별테스트_VOC.fgko      # Forguncy 프로젝트 파일
├── VOC_포건시가져오기용.xlsx     # 가상 고객 문의 20건 + 정답
└── README.md
```

## 시작하기

1. `AI모델별테스트_VOC.fgko`를 Forguncy Builder에서 엽니다.
2. **파일 > 옵션 > 응용 프로그램 설정 > 런타임 기능을 위한 AI 모델**에서 AI 모델 3개를 등록합니다.

   | 설정 이름 | 공급자 유형 | 플랫폼 URL | 모델명 (실험 기준) |
   | --- | --- | --- | --- |
   | Claude | Anthropic | https://api.anthropic.com | claude-sonnet-5 |
   | GPT | OpenAI 호환 | 【URL】 | gpt-6.1-terra |
   | Gemini | OpenAI 호환 | 【URL】 | gemini-3-flash-preview |

3. 각 모델의 **테스트** 버튼으로 연결을 확인합니다.
4. 페이지를 실행하고 **[전체 실행]** 버튼을 누릅니다.

API 키 발급 방법은 아래 가이드를 참고하세요.

- [Claude(Anthropic) 연동 가이드](https://dev.mescius.co.kr/bbs/board.php?bo_table=forguncy_free&wr_id=127)
- [OpenAI(ChatGPT) 연동 가이드](https://dev.mescius.co.kr/bbs/board.php?bo_table=forguncy_free&wr_id=128)
- [Gemini(Google) 연동 가이드](https://dev.mescius.co.kr/bbs/board.php?bo_table=forguncy_free&wr_id=129)

## 동작 방식

모델마다 서버단 명령이 하나씩 있으며, 구성은 같고 **AI 모델 선택만 다릅니다.**

```
파라미터 설정: SumMs = 0, Cnt = 0, OkCnt = 0, Start = NOW()

루프(Loop) 명령 만들기 : =VOC   (반복 항목 파라미터: Item)
   ├ 파라미터 설정: RowStart = NOW()
   ├ AI 도우미 명령 → ClaudeResult
   ├ 파라미터 설정: ElapsedMs = ROUND((NOW()-RowStart)*86400000, 0)
   ├ 파라미터 설정: SumMs = SumMs + ElapsedMs, Cnt = Cnt + 1
   ├ 파라미터 설정: OkCnt = OkCnt + IF(TRIM(ClaudeResult) = Item.정답, 1, 0)
   └ 데이터 테이블 업데이트하기 (조건: ID = Item.ID)

파라미터 설정: TotalMs, AvgMs = SumMs / Cnt, AccRate = OkCnt / Cnt
반환값: TotalMs, AvgMs, OkCnt, AccRate
```

시스템 프롬프트:

```
당신은 고객센터 문의 분류 담당자입니다.
문의를 [배송, 환불/교환, 제품불량, 기술문의, 기타] 중 하나로 분류하세요.
고객이 최종적으로 원하는 처리를 기준으로 판단하고, 분류명 한 단어만 답하세요.
```

## 실험 결과 (3회 평균)

| 모델 | 정확도 | 전체 시간 (20건) | 건당 평균 |
| --- | --- | --- | --- |
| Claude (claude-sonnet-5) | 90% | 22.1초 | 1,093 ms |
| GPT (gpt-6.1-terra) | 100% | 32.1초 | 1,594 ms |
| Gemini (gemini-3-flash-preview) | 95% | 43.1초 | 2,139 ms |

- 측정일: 【실험 날짜】
- 속도와 정확도는 네트워크, AI 서버 상황, 모델 버전에 따라 달라질 수 있습니다.
- 세 모델의 급(균형형/경량형)이 완전히 같지는 않으므로, 회사별 우열이 아닌 이 조합에서의 참고값으로 봐 주세요.

## 주의사항

- **데이터 보안**: AI 도우미 명령을 실행하면 입력 내용이 외부 AI 서비스로 전송됩니다. 샘플 데이터는 모두 가상 데이터이며, 실제 고객 정보로 테스트할 때는 개인정보를 지우고 사용하세요.
- **비용**: 실행할 때마다 API 사용료가 발생합니다. 각 AI 회사 콘솔에서 월 사용 한도를 설정해 두는 것을 권장합니다.
- **모델 지원 종료**: AI 모델은 출시와 종료가 잦습니다. 등록한 모델이 지원 종료되면 런타임 AI 모델 설정에서 모델명만 바꿔 주세요.

## 참고 문서

- [포건시 V12 AI 연결 가이드](https://dev.mescius.co.kr/bbs/board.php?bo_table=forguncy_bntips&wr_id=26)
- [런타임 AI (Forguncy V12 문서)](https://doc.forguncy.co.kr/forguncy/v12/docs/ai/runtime-ai)
- [AI 도우미 명령 (Forguncy V12 문서)](https://doc.forguncy.co.kr/forguncy/v12/docs/ai/runtime-ai/ai-assistant-command)

## 라이선스

【라이선스 정보】
