---

title:  "Algorithm Study"

layout: post

---

# Java

### 두 수 값 비교 (java.lang.Math)
- 큰 수 구하기
![image](https://user-images.githubusercontent.com/52989294/83849978-357d2080-a74b-11ea-8b1f-2da72ec8cee9.png)

- 작은 수 구하기
![image](https://user-images.githubusercontent.com/52989294/83850060-4fb6fe80-a74b-11ea-821b-c57b9023802c.png)

- ex) a와 b 사이의 정수 합을 구하라.
~~~ java
public long solution(int a, int b) {
		long answer = 0;

		for (int i = Math.min(a, b); i <= Math.max(a, b); i++) {
			answer += i;
		}
		return answer;
	}
~~~

***

### 배열 값 비교  (java.util.Array)
![image](https://user-images.githubusercontent.com/52989294/83852013-1df36700-a74e-11ea-89e3-3a728b723041.png)

- 서울에서 김서방 찾기
~~~ java
public String solution(String[] seoul) {
        String answer = "";
	    	
	        for(int i = 0; i< seoul.length ;i++) {
	        	if (seoul[i].equals("Kim")) {
	        		answer = "김서방은 " + i + "에 있다";
                    break;
	        	}
	        }        
	    	
	        return answer;
	    }
~~~

- int형 배열 비교
~~~ java
int[] a = new int[]{1, 2, 3};
int[] b = new int[]{1, 2, 3};
	System.out.println(a.equals(b)); // "false" because a and b refer to different objects
	System.out.println(Arrays.equals(a, b)); //"true" because the elements of a and b have the same values
~~~

- 요소 형이 참조 형인 경우
Arrays.equals() 는 배열 요소에 equals() 를 호출 해 동일성을 판정. 특히 요소 유형 자체가 배열 유형인 경우 ID 비교가 사용됨. 다차원 배열의 동일성을 비교하려면 다음과 같이 Arrays.deepEquals() 사용

~~~ java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};
Object [] aObject = { a };
Object [] bObject = { b };

System.out.println(Arrays.equals(aObject, bObject)); // false
System.out.println(Arrays.deepEquals(aObject, bObject));// true
~~~

***

### 약수의 합
~~~ java
public int solution(int n) {
         int answer = 0;
        for (int i = 1; i <=n ; i++){
            if(n % i == 0){
                answer += i;
            }
        }       
        return answer;
    }
~~~

*** 

### 문자열 char[ ]로 만들기 (java.lang.String) toCharArray()
![image](https://user-images.githubusercontent.com/52989294/83852906-75de9d80-a74f-11ea-95d2-a92f19c702ee.png)

- 문자열 내 p와 y의 개수
~~~ java
boolean solution(String s) {
		boolean answer = true;
		int p_count = 0;
		int y_count = 0;
		char[] stt = s.toCharArray();

		for (char res : stt) {
			if ((res == 'p') || (res == 'P')) {
				p_count++;
			} else if ((res == 'y') || (res == 'Y')) {
				y_count++;
			}
		}

		if (p_count != y_count) {
			answer = false;
		}
		return answer;
	}
~~~

### 문자열 일부 추출 (java.lang.String) substring()
![image](https://user-images.githubusercontent.com/52989294/83856304-4aaa7d00-a754-11ea-925a-ce01f7fa1e1b.png)

~~~ java
String asd = "ABCDE";
System.out.println(asd.substring(1)); // BCDE
System.out.println(asd.substring(1,3)); // BC
~~~


### 문자열에서 index값으로 문자 찾기 (java.lang.String) charAt()

![image](https://user-images.githubusercontent.com/52989294/83857518-11730c80-a756-11ea-9b73-4f8bdf13ee5e.png)

~~~ java
public String solution(String s) {
		String answer = "";
		int length = s.length();

		if (length % 2 != 0) {
			answer = Character.toString(s.charAt(length / 2));
		} else {
			answer = s.substring(length / 2 - 1, length / 2 + 1);
		}
		System.out.println(answer);
		return answer;
	}
~~~
