# Demon Slayer Peon Voice Pack - 제작 가이드

## 목표
귀멸의 칼날(鬼滅の刃) 캐릭터별 peon voice pack을 제작하여 peonping.com 커뮤니티에 배포

## 팩 포맷
- CESP 호환 (openpeon.json + sounds/)
- openpeon.com/create: 팩 생성 가이드
- openpeon.com/spec: CESP 전체 스펙

## 음성 소스

### 1. myinstants.com
팬 커뮤니티 사운드보드. 캐릭터별 짧은 음성 클립(1~5초) 다운로드 가능.

- **URL 패턴**: `https://www.myinstants.com/en/search/?name={캐릭터명}`
- **다운로드**: 각 클립 페이지에서 MP3 URL 확인 → `https://www.myinstants.com/media/sounds/{filename}.mp3`
- **장점**: 이름 붙은 클립이라 내용 파악 쉬움 (예: rengoku-umai, oi-oi-sanemi)
- **단점**: 퀄리티 들쭉날쭉, 일부 캐릭터는 클립 없음
- **활용 캐릭터**: 렌고쿠(16개), 젠이츠(5개), 오바나이(8개), 무이치로(5개), 사네미(2개), 코쿠시보(1개)

### 2. animeclipsraw.fr
애니메이션 음성 클립 아카이브. 캐릭터별 voice 클립 제공.

- **URL 패턴**: `https://animeclipsraw.fr/{캐릭터명}-voice/`
- **다운로드**: 각 클립 페이지에서 Download 링크 (시간제한 토큰 포함)
- **장점**: 깨끗한 음성 클립, 캐릭터 커버리지 넓음
- **단점**: 일부 캐릭터 없음 (도우마, 코쿠시보, 무이치로 404)
- **확인된 캐릭터**: 사네미(5), 텐겐(11), 렌고쿠(15), 젠이츠(5), 교메이(7), 미츠리(6), 오바나이(8)

### 3. YouTube 추출 (yt-dlp + ffmpeg)
YouTube에서 캐릭터 음성 컴필레이션 영상을 다운로드 후 자동 분리.

**워크플로우:**
```bash
# 1. 영상 오디오 다운로드
yt-dlp -x --audio-format wav -o "youtube-extracted/{캐릭터}/full_audio.wav" "{YouTube URL}"

# 2. 자동 클립 분리 (split_clips.py)
cd youtube-extracted && python3 split_clips.py {캐릭터명}
```

**split_clips.py 동작:**
- ffmpeg `silencedetect` 필터로 침묵 구간 감지 (noise=-20dB, min_silence=0.3초)
- 침묵 경계에서 음성 분리, 2~5초 범위의 클립만 유지
- `loudnorm` 정규화 (-16 LUFS) 적용
- 출력: `{캐릭터명}-NN.mp3` 형태

**장점**: 가장 고품질, 원본 애니 음성 그대로
**단점**: 영상 URL 직접 찾아야 함, BGM/SE 혼입 가능성

**YouTube 검색 팁:**
- 일본어: `鬼滅の刃 {캐릭터 한자} セリフ集` (예: `鬼滅の刃 煉獄杏寿郎 セリフ集`)
- 영어: `demon slayer {캐릭터} voice lines compilation japanese`

## 대상 캐릭터 (16명)

| 캐릭터 | 팩 이름 | 클립 수 | 성우 (JP) |
|--------|---------|---------|-----------|
| 카마도 탄지로 | demon-slayer-tanjiro | 20 | 하나에 나츠키 |
| 카마도 네즈코 | demon-slayer-nezuko | 16 | 키토 아카리 |
| 렌고쿠 쿄주로 | demon-slayer-rengoku | 18 | 히노 사토시 |
| 하시비라 이노스케 | demon-slayer-inosuke | 6 | 마츠오카 요시츠구 |
| 아가츠마 젠이츠 | demon-slayer-zenitsu | 11 | 시모노 히로 |
| 키부츠지 무잔 | demon-slayer-muzan | 23 | 세키토시카즈 |
| 아카자 | demon-slayer-akaza | 11 | 이시다 아키라 |
| 코쵸 시노부 | demon-slayer-shinobu | 26 | 하야미 사오리 |
| 토미오카 기유 | demon-slayer-giyu | 9 | 사쿠라이 타카히로 |
| 칸로지 미츠리 | demon-slayer-mitsuri | 6 | 하나자와 카나 |
| 우즈이 텐겐 | demon-slayer-tengen | 11 | 코니시 카츠유키 |
| 시나즈가와 사네미 | demon-slayer-sanemi | 7 | 세키 토모카즈 |
| 토키토 무이치로 | demon-slayer-muichiro | 5 | - |
| 이구로 오바나이 | demon-slayer-obanai | 16 | - |
| 히메지마 교메이 | demon-slayer-gyomei | 7 | 스기타 토모카즈 |
| 코쿠시보 | demon-slayer-kokushibo | 1 | 오키아유 료타로 |

## CESP 카테고리 매핑 가이드

클립을 4개 카테고리에 배치할 때 캐릭터 성격에 맞게 분류:

| 카테고리 | 용도 | 배치 기준 |
|----------|------|----------|
| session.start | 세션 시작 시 재생 | 자기소개, 인사, 등장 대사 |
| task.complete | 작업 완료 시 재생 | 승리, 기합, 기술 발동, 긍정 반응 |
| task.error | 에러 발생 시 재생 | 분노, 위협, 패닉, 부정 반응 |
| input.required | 사용자 입력 필요 시 | 질문, 확인 요청, 알림음 |

## openpeon.json 템플릿

```json
{
  "cesp_version": "1.0",
  "name": "demon-slayer-{캐릭터}",
  "display_name": "Demon Slayer - {캐릭터 영문명}",
  "version": "1.0.0",
  "description": "{캐릭터 영문명} voice pack from Demon Slayer (Kimetsu no Yaiba) - Fan community voice pack",
  "author": {
    "name": "djlee",
    "github": "djlee"
  },
  "license": "fan-community",
  "language": "ja",
  "categories": {
    "session.start": { "sounds": [] },
    "task.complete": { "sounds": [] },
    "task.error": { "sounds": [] },
    "input.required": { "sounds": [] }
  }
}
```

## 제작 워크플로우

### Phase 1: 클립 수집
1. myinstants.com에서 캐릭터명 검색 → 클립 다운로드
2. animeclipsraw.fr에서 캐릭터 voice 페이지 확인 → 클립 다운로드
3. (선택) YouTube에서 음성 컴필레이션 영상 찾기 → yt-dlp + split_clips.py로 추출

### Phase 2: 정리 및 매핑
4. 클립 청취 후 카테고리 분류 (session.start / task.complete / task.error / input.required)
5. 불필요한 클립 제거 (BGM 혼입, 너무 짧은/긴 클립, 중복)
6. openpeon.json 작성

### Phase 3: 배포
7. 클립 들어보며 최종 QA
8. peonping.com에 팩 업로드

## 클립 부족 캐릭터 보강 방법
- **코쿠시보** (1개): YouTube에서 `鬼滅の刃 黒死牟 セリフ` 검색하여 추출
- **무이치로** (5개): animeclipsraw.fr에 없음, YouTube 추출 권장
- **이노스케** (6개): YouTube full_audio.wav에서 BGM만 나왔음, 다른 영상 필요

## 라이선스
- fan-community (팬메이드 커뮤니티 배포)
- 짧은 음성 클립(2~5초), 비상업적 무료 공유

## 참고 사이트
- openpeon.com/create: 팩 생성 가이드
- openpeon.com/spec: CESP 전체 스펙
- myinstants.com: 팬 사운드보드
- animeclipsraw.fr: 애니 음성 클립 아카이브
- 101soundboards.com: AI TTS 기반 사운드보드 (참고용)
