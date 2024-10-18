# [729. My Calendar I](https://leetcode.com/problems/my-calendar-i/)

You are implementing a program to use as your calendar. We can add a new event if adding the event will not cause a **double booking**.

A **double booking** happens when two events have some non-empty intersection (i.e., some moment is common to both events.).

The event can be represented as a pair of integers `start` and `end` that represents a booking on the half-open interval `[start, end)`, the range of real numbers `x` such that `start <= x < end`.

Implement the `MyCalendar` class:

- `MyCalendar()` Initializes the calendar object.
- `boolean book(int start, int end)` Returns `true` if the event can be added to the calendar successfully without causing a **double booking**. Otherwise, return `false` and do not add the event to the calendar.

**Example 1:**

**Input**
```
["MyCalendar", "book", "book", "book"]
[[], [10, 20], [15, 25], [20, 30]]
```
**Output**
```
[null, true, false, true]
```
**Explanation**
```
MyCalendar myCalendar = new MyCalendar();
myCalendar.book(10, 20); // return True
myCalendar.book(15, 25); // return False, It can not be booked because time 15 is already booked by another event.
myCalendar.book(20, 30); // return True, The event can be booked, as the first event takes every time less than 20, but not including 20.
```

**Constraints:**

- $0 <= start < end <= 10^9$
- At most `1000` calls will be made to `book`.

## 1. np.array로 예약 관리
```python
import numpy as np

class MyCalendar:

    def __init__(self):
        self.days = np.zeros(10**9)

    def book(self, start: int, end: int) -> bool:
        if np.all(self.days[start:end] == 0):
            self.days[start:end] = 1
            return True
        else:
            return False
```

- 실패 사유 : 메모리 초과

## 2. 시작점과 끝점 비교
```python
class MyCalendar:

    def __init__(self):
        self.bookings = []

    def book(self, start: int, end: int) -> bool:
        for s, e in self.bookings:
            if start < e and end > s :
                return False

        self.bookings.append((start,end))
        return True

```
- 통과
	- 307 ms
		- 53.47%
	- 17.28 MB
		- 91.21%


## 소화할 것
```python
class MyCalendar:

    def __init__(self):
        self.calendar = []

    def book(self, start: int, end: int) -> bool:
        # Find the position to insert the new event using binary search
        i = bisect_left(self.calendar, (start, end))
        
        # Check for overlap with the previous event
        if i > 0 and self.calendar[i - 1][1] > start:
            return False
        
        # Check for overlap with the next event
        if i < len(self.calendar) and end > self.calendar[i][0]:
            return False
        
        # Insert the new event if no overlaps
        self.calendar.insert(i, (start, end))
        return True
```
- bisect_left로 start를 기준으로 가능한 인덱스를 이진탐색
	- 탐색된 인덱스의 직전 이벤트 종료일과 비교해 가능 여부 확인
	- 다음 이벤트 시작일과 비교해 가능여부 확인
- 조건을 모두 통과 한 경우 캘린더에 추가 및 True 반환.