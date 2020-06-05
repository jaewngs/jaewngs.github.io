---

title:  "Algorithm Study"

layout: post

---

# Java

## [ 두 수 값 비교 ]
- 큰 수 구하기
![image](https://user-images.githubusercontent.com/52989294/83849978-357d2080-a74b-11ea-8b1f-2da72ec8cee9.png)

- 작은 수 구하기
![image](https://user-images.githubusercontent.com/52989294/83850060-4fb6fe80-a74b-11ea-821b-c57b9023802c.png)

### ex) a와 b 사이의 정수 합을 구하라.
~~~ java
public long solution(int a, int b) {
		long answer = 0;

		for (int i = Math.min(a, b); i <= Math.max(a, b); i++) {
			answer += i;
		}
		return answer;
	}
~~~