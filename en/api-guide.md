## Application Service > Spell Checker > API Guide

Spell Checker를 사용하는데 필요한 API를 설명합니다.

## 오탈자 검사

Spell Checker API에 맞춤법을 검사할 문장(이하, 질의)을 전달하여 오탈자를 검사하고 교정어를 추천받을 수 있습니다. 입력 질의에는 500자 제한이 있으며, 500자가 넘으면 별다른 경고 없이 잘려서 처리됩니다. 때문에 질의 입력창에서 500자 이상 입력하지 못하도록 제한할 필요가 있습니다. API가 탐지한 오탈자는 입력 질의에 등장하는 순서대로 정렬되어 있으며, 각 오탈자의 추천어들은 Spell Checker가 판단한 가장 적합한 추천어 순으로 정렬되어 있습니다. Spell Checker는 영문 및 기타 언어의 오탈자 검사를 지원하지 않습니다.

### 요청

##### URI
| 메서드 | URI |
|:---|:---|
| POST | http://api-spell-checker.cloud.toast.com/spell-checker/v1.0/appkeys/{appkey}/corrections |

##### 요청 헤더
| 이름 | 설명 |
|:---|:---|
| Content-Type | application/json |

##### 요청 본문
```
{
    "query": String,
    "maxSuggestion": int
}
```

##### 필드
| 이름 | 유형 | 필수 여부 | 유효 범위 | 설명 |
|:---|:---|:---|:---|:---|
| query | String | 필수 | 최대 500자 | 질의 |
| maxSuggestion | int | 필수 | 0~16 | 반환받을 추천 교정어의 최대 개수 |

### 응답

##### 응답 본문
```
{
    "header": {
        "isSuccessful": boolean,
        "resultCode": int,
        "resultMessage": String
    },
    "body": {
        "query": String,
        "maxSuggestion": int,
        "errata" : [
            {
                "erratum": String,
                "position": int,
                "length": int,
                "suggestions": [
                    {
                        "suggestion": String,
                        "type": int
                    },
                    ...
                ]
            },
            ...
        ],
        "elapsedTime": int
    }
}
```

##### 필드
| 필드 | 유형 | 필수 여부 | 설명 |
|:---|:---|:---|:---|
| query | String | | 질의 |
| maxSuggestion | int |  | 추천 교정어 최대 개수(값 범위: 0~16) |
| errata | List | | 검사된 오탈자 목록 |
| erratum | String | | 검사된 오탈자 |
| position | int | |  오탈자의 바이트 시작 위치 |
| length | int | | 오탈자의 바이트 길이(문자 당 길이: 영문 - 1, UTF8 - 3) |
| suggestions | List | | 오탈자의 교정 추천어 목록(추천 랭킹 오름차순 정렬) |
| suggestion | String | | 오탈자의 교정 추천어 |
| type | int | | 추천어의 교정 방식<br>1: 맞춤법<br>2: 띄어쓰기<br>3: 표준어 추정 |
| elaspedTime | int | | API 오탈자 분석 소요 시간 |


## 참고 사항

### 응답속도를 높이기 위한 가이드

입력 질의의 500자 길이 제한은 맞춤법 검사기의 처리 성능이 입력 문장의 길이 및 오탈자의 개수와 밀접한 연관이 있는 점을 감안해 설정한 것입니다. 검사 문장이 짧을 수록 성능 면에서 유리하기 때문에, 500자 제한이 있지만 그보다 짧은 입력 유도를 권해드립니다. 예를 들어 전체 문장을 받아 한 번에 맞춤법 검사를 실행해 주는 방법보다는, 문장을 입력받을 때마다 실시간으로, 또는 주기적으로 문장을 부분적으로 끊어서 검사하는 방법으로 프로그램을 설계하시는 편이 성능 면에서 더 유리합니다.