# [신고 결과 받기(2022 KAKAO BLIND RECRUITMENT)](https://school.programmers.co.kr/learn/courses/30/lessons/92334)

> 문제 설명

신입사원 무지는 게시판 불량 이용자를 신고하고 처리 결과를 메일로 발송하는 시스템을 개발하려 합니다. 무지가 개발하려는 시스템은 다음과 같습니다.

- 각 유저는 한 번에 한 명의 유저를 신고할 수 있습니다.
    - 신고 횟수에 제한은 없습니다. 서로 다른 유저를 계속해서 신고할 수 있습니다.
    - 한 유저를 여러 번 신고할 수도 있지만, 동일한 유저에 대한 신고 횟수는 1회로 처리됩니다.
- k번 이상 신고된 유저는 게시판 이용이 정지되며, 해당 유저를 신고한 모든 유저에게 정지 사실을 메일로 발송합니다.
    - 유저가 신고한 모든 내용을 취합하여 마지막에 한꺼번에 게시판 이용 정지를 시키면서 정지 메일을 발송합니다.

다음은 전체 유저 목록이 ["muzi", "frodo", "apeach", "neo"]이고, k = 2(즉, 2번 이상 신고당하면 이용 정지)인 경우의 예시입니다.

| 유저 ID | 유저가 신고한 ID | 설명 |
| --- | --- | --- |
| "muzi" | "frodo" | "muzi"가 "frodo"를 신고했습니다. |
| "apeach" | "frodo" | "apeach"가 "frodo"를 신고했습니다. |
| "frodo" | "neo" | "frodo"가 "neo"를 신고했습니다. |
| "muzi" | "neo" | "muzi"가 "neo"를 신고했습니다. |
| "apeach" | "muzi" | "apeach"가 "muzi"를 신고했습니다. |

각 유저별로 신고당한 횟수는 다음과 같습니다.

| 유저 ID | 신고당한 횟수 |
| --- | --- |
| "muzi" | 1 |
| "frodo" | 2 |
| "apeach" | 0 |
| "neo" | 2 |

위 예시에서는 2번 이상 신고당한 "frodo"와 "neo"의 게시판 이용이 정지됩니다. 이때, 각 유저별로 신고한 아이디와 정지된 아이디를 정리하면 다음과 같습니다.

| 유저 ID | 유저가 신고한 ID | 정지된 ID |
| --- | --- | --- |
| "muzi" | ["frodo", "neo"] | ["frodo", "neo"] |
| "frodo" | ["neo"] | ["neo"] |
| "apeach" | ["muzi", "frodo"] | ["frodo"] |
| "neo" | 없음 | 없음 |

따라서 "muzi"는 처리 결과 메일을 2회, "frodo"와 "apeach"는 각각 처리 결과 메일을 1회 받게 됩니다.

이용자의 ID가 담긴 문자열 배열 `id_list`, 각 이용자가 신고한 이용자의 ID 정보가 담긴 문자열 배열 `report`, 정지 기준이 되는 신고 횟수 `k`가 매개변수로 주어질 때, 각 유저별로 처리 결과 메일을 받은 횟수를 배열에 담아 return 하도록 solution 함수를 완성해주세요.

---

### 제한사항

- 2 ≤ `id_list`의 길이 ≤ 1,000
    - 1 ≤ `id_list`의 원소 길이 ≤ 10
    - `id_list`의 원소는 이용자의 id를 나타내는 문자열이며 알파벳 소문자로만 이루어져 있습니다.
    - `id_list`에는 같은 아이디가 중복해서 들어있지 않습니다.
- 1 ≤ `report`의 길이 ≤ 200,000
    - 3 ≤ `report`의 원소 길이 ≤ 21
    - `report`의 원소는 "이용자id 신고한id"형태의 문자열입니다.
    - 예를 들어 "muzi frodo"의 경우 "muzi"가 "frodo"를 신고했다는 의미입니다.
    - id는 알파벳 소문자로만 이루어져 있습니다.
    - 이용자id와 신고한id는 공백(스페이스)하나로 구분되어 있습니다.
    - 자기 자신을 신고하는 경우는 없습니다.
- 1 ≤ `k` ≤ 200, `k`는 자연수입니다.
- return 하는 배열은 `id_list`에 담긴 id 순서대로 각 유저가 받은 결과 메일 수를 담으면 됩니다.

---

### 입출력 예

| id_list | report | k | result |
| --- | --- | --- | --- |
| ["muzi", "frodo", "apeach", "neo"] | ["muzi frodo","apeach frodo","frodo neo","muzi neo","apeach muzi"] | 2 | [2,1,1,0] |
| ["con", "ryan"] | ["ryan con", "ryan con", "ryan con", "ryan con"] | 3 | [0,0] |

#

> 해결

```jsx
function solution(id_list, report, k) {
    let answer = []
    let idMap = {}
    // idMap 객체는 유저의 id를 key로 가지며, 신고 대상과 자신이 신고 받은 횟수를 저장
    id_list.forEach(id => idMap = {...idMap, [id]: {targetList: [], reported: 0}})
    report.forEach(el => {
        const [id, target] = el.split(" ")
        if (!idMap[id].targetList.includes(target)) {  // 이미 신고한 대상이 아니면
            idMap[id].targetList.push(target)          // 대상 리스트에 추가하고
            idMap[target].reported++                   // 대상의 신고 받은 횟수를 1 증가
        }
    })
    id_list.forEach(id => {
        let count = 0
        idMap[id].targetList.forEach(target => {
            // key에 저장된 신고 대상의 신고 받은 횟수가 k 이상이면 count(받을 메일 수)를 1 증가
            if (idMap[target].reported >= k) count++
        })
        answer.push(count)
    })
    return answer
}
```

| 케이스 | 결과 |
| --- | --- |
| 테스트 3 〉 | 통과 (372.08ms, 81.7MB) |
| 테스트 7 〉 | 통과 (6.29ms, 38MB) |
| 테스트 8 〉 | 통과 (9.66ms, 38.2MB) |
| 테스트 9 〉 | 통과 (138.90ms, 54.5MB) |
| 테스트 10 〉 | 통과 (136.47ms, 54MB) |
| 테스트 11 〉 | 통과 (373.20ms, 82.6MB) |
| 테스트 14 〉 | 통과 (211.07ms, 58.2MB) |
| 테스트 15 〉 | 통과 (306.49ms, 65.2MB) |
| 테스트 20 〉 | 통과 (213.41ms, 58.6MB) |
| 테스트 21 〉 | 통과 (300.44ms, 64.3MB) |

#

> 효율 개선

```jsx
function solution(id_list, report, k) {
    let idMap = {}
    // targetList는 중복되지 않아야하므로 Set으로 변경
    id_list.forEach(id => idMap[id] = {targetList: new Set(), reported: 0})
    for (let i = 0; i < report.length; i++) {
        const [id, target] = report[i].split(" ")
	const isDuplicate = idMap[id].targetList.size === idMap[id].targetList.add(target).size
        idMap[target].reported += isDuplicate ? 0 : 1
    }
    return id_list.map(id => [...idMap[id].targetList].reduce((acc, target) => acc + (idMap[target].reported >= k), 0))
}
```

| 케이스 | 결과 | 시간 증감 | 메모리 증감 |
| --- | --- | --- | --- |
| 테스트 3 〉 | 통과 (207.54ms, 87.1MB) | -164.54ms | +5.4MB |
| 테스트 7 〉 | 통과 (8.18ms, 38.1MB) | +1.89ms | +0.1MB |
| 테스트 8 〉 | 통과 (10.78ms, 38.3MB) | +1.12ms | +0.1MB |
| 테스트 9 〉 | 통과 (83.86ms, 63.4MB) | -55.04ms | +8.9MB |
| 테스트 10 〉 | 통과 (79.18ms, 58MB) | -57.29ms | +4MB |
| 테스트 11 〉 | 통과 (174.91ms, 87.2MB) | -198.29ms | +4.6MB |
| 테스트 14 〉 | 통과 (86.11ms, 58.3MB) | -124.96ms | +0.1MB |
| 테스트 15 〉 | 통과 (168.62ms, 67.8MB) | -137.87ms | +2.6MB |
| 테스트 20 〉 | 통과 (112.00ms, 58.1MB) | -101.41ms | -0.5MB |
| 테스트 21 〉 | 통과 (154.54ms, 67.9MB) | -145.90ms | +3.6MB |
