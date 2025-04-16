---
title: ⏰ 예약 불가 시간대에 접근할 수 있는 이슈
date: 2025-03-01 00:00:01
categories:
- 트러블슈팅
- 꿈it(잇)다
tag:
- 트러블슈팅
- 꿈it(잇)다
---

# 개요
- AI 모의면접 시간 중 **예약 시간이 지난 시간을 선택하여 예약할 수 있는 이슈**를 발견.

# 분석
- 백단에서 스케줄러를 통해 예약 가능 여부를 갱신하고 있었으나, 브라우저 새로고침 없이 실시간 반영이 되지 않아 발생한 문제.

# 해결 방법
- 화면이 리렌더링이 될 때 즉, 날짜를 선택하거나 예약 리스트 호출 시 예약 가능한 시간과 현재 시간을 비교하여 
  버튼에 disabled 속성을 동적으로 제어하는 것을 추가
- **isTimePassed 함수 추가 후 disabled 설정**

```javascript
// 예약 시간 조회하는 params
const [timeParams, setTimeParams] = useState({
  searchYear: date instanceof Date ? `${date.getFullYear()}` : `${today.getFullYear()}`,
  searchMonth:
          date instanceof Date ? `${date.getMonth() + 1}`.padStart(2, '0') : `${today.getMonth() + 1}`.padStart(2, '0'),
  searchDay: date instanceof Date ? `${date.getDate()}`.padStart(2, '0') : `${today.getDate()}`.padStart(2, '0'),
});

// 달력에서 날짜 변경 시 예약 시간 api 호출
useEffect(() => {
  if (date instanceof Date) {
    setTimeParams({
      searchYear: `${date.getFullYear()}`,
      searchMonth: `${date.getMonth() + 1}`.padStart(2, '0'),
      searchDay: `${date.getDate()}`.padStart(2, '0'),
    });
  }
}, [date]);

// 예약 시간 api 호출
const {
  data: reservationTime,
  error: reservationTimeErr,
  isLoading: reservationTimeLoading,
} = useAiInterviewTimeApi(timeParams);
const reservationTimeList = reservationTime?.data?.resultList!;

// 시간 비교하는 함수
const isTimePassed = (time: string) => {
  if (date instanceof Date && date.toDateString() === today.toDateString()) {
    const currentTime = today.getHours() * 60 + today.getMinutes();
    const [hours, minutes] = time.split(':').map(Number);
    const itemTime = hours * 60 + minutes;
    return itemTime <= currentTime;
  }
  return false;
};

return (
  <ol>
    {reservationTImeList?.map((item, index) => {
      return (
        <li key={item.rsvtNo}>
          <label>
            <input
              type="radio"
              disabled={
                isTimePassed(item.noBgngHm)
              }
            />
            <span>
              {item.rsvtNo}차 {item.noBgngHm} ({item.noRsvtCnt}/{item.maxNoRsvtCnt})
            </span>
          </label>
        </li>
      )
    })}
  </ol>
)
```

# 결과
- 프론트에서 시간을 비교 후 버튼에 disabled 속성을 주어서 이중으로 클릭을 막으니, 예약 불가 시간대 접근하는 횟수가 현저히 줄어든 것을 확인.

백단에서 값을 갱신하여 준다고 하더라도 때에 따라서는 프론트단에서 이중으로 막아 사용자들의 ux를 개선하고 혹시 모를 오류에 대비를 해야되는 걸 
또 한번 느끼게 되었다. 안일하게 생각하지말고 사용자의 불편함을 최소화하기 위해 UI/UX 개선에 더 신경을 써야겠다.