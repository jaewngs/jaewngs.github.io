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
	- Arrays.equals() 는 배열 요소에 equals() 를 호출 해 동일성을 판정. 특히 요소 유형 자체가 배열 유형인 경우 ID 비교가 사용됨. 다차원 배열의 동일성을 비교하려면 다음과 같이 Arrays.deepEquals() 사용

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

***

### char[]을 String로 바꾸기 (java.lang.String) valueOf(char[] data)

![image](https://user-images.githubusercontent.com/52989294/83968830-dcf28280-a906-11ea-8fd4-773bbde662b3.png)

- 전화번호가 문자열 phone_number로 주어졌을 때, 전화번호의 뒷 4자리를 제외한 나머지 숫자를 전부 *으로 가린 문자열을 리턴하는 함수

	~~~ java
class Solution {
   public String solution(String phone_number) {
		String answer = "";

		char aaa[] = phone_number.toCharArray();

		for (int i = 0; i < phone_number.length() - 4; i++) {
			aaa[i] = '*';
		}

		answer = String.valueOf(aaa);

		return answer;
	}
}
	~~~
    
***

### 문자열 일부 추출 (java.lang.String) substring()
![image](https://user-images.githubusercontent.com/52989294/83856304-4aaa7d00-a754-11ea-925a-ce01f7fa1e1b.png)

~~~ java
String asd = "ABCDE";
System.out.println(asd.substring(1)); // BCDE
System.out.println(asd.substring(1,3)); // BC
~~~

***

### 문자열에서 index값으로 문자 찾기 (java.lang.String) charAt()

![image](https://user-images.githubusercontent.com/52989294/83857518-11730c80-a756-11ea-9b73-4f8bdf13ee5e.png)

* 가운데 글자 가져오기
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

***

### ArrayList (java.util.ArrayList)
- 메모리가 허용하는 한 자동으로 ArrayList 크기는 동적으로 변경됨

- 같은 숫자는 싫어
:배열 arr의 각 원소는 숫자 0부터 9까지로 이루어져 있습니다. 이때, 배열 arr에서 연속적으로 나타나는 숫자는 하나만 남기고 전부 제거하려고 합니다. 단, 제거된 후 남은 수들을 반환할 때는 배열 arr의 원소들의 순서를 유지해야 합니다. 예를 들면,

	arr = [1, 1, 3, 3, 0, 1, 1] 이면 [1, 3, 0, 1] 을 return 합니다.
	arr = [4, 4, 4, 3, 3] 이면 [4, 3] 을 return 합니다.

	~~~ java
	import java.util.*;

		public class Solution {
	 	   public int[] solution(int []arr) {
 		       int[] answer = {};
 		       List<Integer> arrList = new ArrayList<>();
 		       int temp = 10;

 	 	      for(int num : arr){
  	  	        if(temp!=num)
  	   	           arrList.add(num);
  	  	        temp = num;
  	 	     }

 		       answer = new int[arrList.size()];
    	  	  for (int i = 0; i < arrList.size(); i++) {
    	  	      answer[i] = arrList.get(i);
     		   }
    		    return answer;
  		  }
		}
	~~~
    
***

### Long (java.lang.Long) valueOF()
![image](https://user-images.githubusercontent.com/52989294/83936727-52742b00-a801-11ea-83b0-86c4397f7072.png)

- 정수 x와 자연수 n을 입력 받아, x부터 시작해 x씩 증가하는 숫자를 n개 지니는 리스트를 리턴

	~~~ java
   class Solution {
   public long[] solution(long x, int n) {
		long[] answer =  new long[n];
		
		for (int i = 0; i < n; i++) {
			answer[i] = (Long.valueOf(x) * (i+1));
            // answer[i] = ( x * (i+1));
		}

		return answer;
	}
}
	~~~

- valueOf(String s, int radix)
	~~~ java
    Long aaa = Long.valueOf("1111",2); // 8+4+2+1
    ~~~

***

### Integer (java.lang.Integer) valueOF()

![image](https://user-images.githubusercontent.com/52989294/83936795-df1ee900-a801-11ea-9b77-ac1e3d8d6e72.png)

***
자릿수 더하기

















