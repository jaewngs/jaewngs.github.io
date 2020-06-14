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
- arr = [1, 1, 3, 3, 0, 1, 1] 이면 [1, 3, 0, 1] 을 return 합니다.
- arr = [4, 4, 4, 3, 3] 이면 [4, 3] 을 return 합니다.

~~~ java
import java.util.*;

public class Solution {
	public int[] solution(int[] arr) {
		int[] answer = {};
		List<Integer> arrList = new ArrayList<>();
		int temp = 10;

		for (int num : arr) {
			if (temp != num)
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

### 자릿수 더하기
- 자연수 N이 주어지면, N의 각 자릿수의 합을 구해서 return 하는 solution 함수를 만들어 주세요. 예를들어 N = 123이면 1 + 2 + 3 = 6을 return 하면 됩니다.

	~~~ java
	import java.util.*;

	public class Solution {
	    public int solution(int n) {
	        int answer = 0;
	        while(n !=0){
	            answer += (n%10);
	            n /= 10;
	        }
	        return answer;
	    }
	}
	~~~

***

### x만큼 간격이 있는 n개의 숫자
- 함수 solution은 정수 x와 자연수 n을 입력 받아, x부터 시작해 x씩 증가하는 숫자를 n개 지니는 리스트를 리턴해야 합니다.
	~~~ java
	class Solution {
	   public long[] solution(long x, int n) {
			long[] answer =  new long[n];

			for (int i = 0; i < n; i++) {
				answer[i] = (x * (i+1));
			}
			return answer;
		}
	}
	~~~

***

### 직사각형 별찍기
- 이 문제에는 표준 입력으로 두 개의 정수 n과 m이 주어집니다. 별(*) 문자를 이용해 가로의 길이가 n, 세로의 길이가 m인 직사각형 형태를 출력해보세요.

	~~~ java
    import java.util.Scanner;

	public class Solution {
	    public static void main(String[] args) {
	        Scanner sc = new Scanner(System.in);
	        int a = sc.nextInt();
	        int b = sc.nextInt();

 	       for (int i = 0; i < b; i++) {
				for (int j = 0; j < a; j++) {
				System.out.print("*");
				}
				System.out.println();
			}
  	  }
	}
   	~~~

***

### 평균 구하기
- 정수를 담고 있는 배열 arr의 평균값을 return하는 함수, solution을 완성해보세요.
	~~~ java
	class Solution {
	    public double solution(int[] arr) {
	        int len = arr.length;
			double sum = 0;
			double avg = 0;

			for (int i = 0; i < len; i++) {
				sum += arr[i];
			}

			avg = sum / len;
			return avg;
	    }
	}
	~~~

***

### 행렬의 덧셈
- 행렬의 덧셈은 행과 열의 크기가 같은 두 행렬의 같은 행, 같은 열의 값을 서로 더한 결과가 됩니다. 2개의 행렬 arr1과 arr2를 입력받아, 행렬 덧셈의 결과를 반환하는 함수, solution을 완성해주세요.

	~~~ java
	class Solution {
	    public int[][] solution(int[][] arr1, int[][] arr2) {
	        int[][] answer = new int[arr1.length][arr1[0].length];

			for (int i = 0; i < arr1.length; i++) {
				for (int j = 0; j < arr1[i].length; j++) {
					answer[i][j] = arr1[i][j] + arr2[i][j];
				}
			}
			return answer;
 	   }
	}
	~~~

***

### 최대공약수와 최소공배수
- 두 수를 입력받아 두 수의 최대공약수와 최소공배수를 반환하는 함수, solution을 완성해 보세요. 배열의 맨 앞에 최대공약수, 그다음 최소공배수를 넣어 반환하면 됩니다. 예를 들어 두 수 3, 12의 최대공약수는 3, 최소공배수는 12이므로 solution(3, 12)는 [3, 12]를 반환해야 합니다.

	~~~ java
	class Solution {
	    public int[] solution(int n, int m) {
	      int[] answer = new int[2];        // 최대 공약수, 최소 공배수를 담을 배열
	      int tmp1,tmp2;                    // n,m을 담을 변수
	      int r=1;                          // 큰수를 작은수로 나눈 나머지
  	                                      // 반복문의 조건을 위해 초기값1
  	    if(n>m){                          // 큰 수를 찾는 조건
   	       tmp1 = n;                     // 큰수
  	        tmp2 = m;                     // 작은수
 	     }else{                            // 반대의 경우
  	        tmp1 = m;                     // 큰수
 	         tmp2 = n;                     // 작은수
 	     }

   	   while(r>0){                       // 나머지가 0이상일때까지
   	       r=tmp1%tmp2;                  //큰수 나누기 작은수의 나머지
  	        tmp1 = tmp2;                  //큰수에 작은수 대입
  	        tmp2 = r;                     //작은수에 나머지 대입
	      }
	      answer[0]= tmp1;                  // 최대 공약수
	      answer[1]=n*m/tmp1;               // 최소 공배수
	      return answer;
	  }
	}
	~~~

***

### 정수 제곱근 판별
- 임의의 양의 정수 n에 대해, n이 어떤 양의 정수 x의 제곱인지 아닌지 판단하려 합니다.
n이 양의 정수 x의 제곱이라면 x+1의 제곱을 리턴하고, n이 양의 정수 x의 제곱이 아니라면 -1을 리턴하는 함수를 완성하세요.

	~~~ java
	import java.util.*;

	class Solution {
	    public long solution(long n) {
	       double sqrt = Math.sqrt(n);

	        if(sqrt == (int)sqrt){
	            return (long)Math.pow(sqrt+1, 2);
 	       }
  	      else return -1;
  	  }
	}
	~~~

***

### 문자열 다루기 기본
- 문자열 s의 길이가 4 혹은 6이고, 숫자로만 구성돼있는지 확인해주는 함수, solution을 완성하세요. 예를 들어 s가 a234이면 False를 리턴하고 1234라면 True를 리턴하면 됩니다.

	~~~ java
	class Solution {
		public boolean solution(String s) {
			if (s.length() == 4 || s.length() == 6) {
				try {
					int x = Integer.parseInt(s);
					return true;
				} catch (NumberFormatException e) {
					return false;
				}
			} else
				return false;
		}
	}
	~~~

***

### 정수 내림차순으로 배치하기
- 함수 solution은 정수 n을 매개변수로 입력받습니다. n의 각 자릿수를 큰것부터 작은 순으로 정렬한 새로운 정수를 리턴해주세요. 예를들어 n이 118372면 873211을 리턴하면 됩니다.

    ~~~ java
    import java.util.Arrays;
    import java.util.Collections;

    class Solution {
      public long solution(long n) {
            long answer = 0;
            // long형 문자열로 만들고 한글자씩 분할 후 전체 길이 저장
            int len = Long.toString(n).split("").length;

            // 문자열 배열 생성
            String[] array = new String[len];

            // 입력받은 정수 문자열로 받아서 한글자씩 분할해서 array에 저장
            array = Long.toString(n).split("");

            String tmp = "";

            // 내림차순 정렬
            Arrays.sort(array, Collections.reverseOrder());

            for (int i = 0; i < len; i++) {
                tmp += (long) Integer.parseInt(array[i]);
            }
            answer = Long.parseLong(tmp);

            return answer;
        }
    }
    ~~~

***

### 자연수 뒤집어 배열로 만들기
- 자연수 n을 뒤집어 각 자리 숫자를 원소로 가지는 배열 형태로 리턴해주세요. 예를들어 n이 12345이면 [5,4,3,2,1]을 리턴합니다.

    ~~~ java
    class Solution {
       public int[] solution(long n) {

            String sss = Long.toString(n);
            char[] answer = sss.toCharArray();
            int[] res = new int[answer.length];
            int j = 0;
            for (int i = answer.length - 1; i >= 0; i--) {
                res[i] = Character.getNumericValue(answer[j]);
                j++;
            }
            System.out.println(res[0]);
            return res;
        }
    }
    ~~~

***

### 핸드폰 번호 가리기
- 프로그래머스 모바일은 개인정보 보호를 위해 고지서를 보낼 때 고객들의 전화번호의 일부를 가립니다. 전화번호가 문자열 phone_number로 주어졌을 때, 전화번호의 뒷 4자리를 제외한 나머지 숫자를 전부 *으로 가린 문자열을 리턴하는 함수, solution을 완성해주세요.

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

### K번째 수
- 배열 array의 i번째 숫자부터 j번째 숫자까지 자르고 정렬했을 때, k번째에 있는 수를 구하려 합니다. 예를 들어 array가 [1, 5, 2, 6, 3, 7, 4], i = 2, j = 5, k = 3이라면 array의 2번째부터 5번째까지 자르면 [5, 2, 6, 3]입니다. 1에서 나온 배열을 정렬하면 [2, 3, 5, 6]입니다. 2에서 나온 배열의 3번째 숫자는 5입니다.
- 배열 array, [i, j, k]를 원소로 가진 2차원 배열 commands가 매개변수로 주어질 때, commands의 모든 원소에 대해 앞서 설명한 연산을 적용했을 때 나온 결과를 배열에 담아 return 하도록 solution 함수를 작성해주세요.

![image](https://user-images.githubusercontent.com/52989294/84586966-5f72c900-ae56-11ea-8e15-ebb53ed907c2.png)

~~~ java
import java.util.*;
class Solution {
    public int[] solution(int[] array, int[][] commands) {
        int[] answer = new int[commands.length];
        int index = 0;

        for (int i = 0; i < commands.length; i++) {
            int start = commands[i][0];
            int end = commands[i][1];
            int k = commands[i][2];

            int[] tmp = new int[end - start + 1];

            int a = 0;
            for (int j = start - 1; j < end; j++)
                tmp[a++] = array[j];

            Arrays.sort(tmp);
            answer[index++] = tmp[k - 1];
        }
        return answer;
    }
}
~~~

***

### 하샤드 수
- 양의 정수 x가 하샤드 수이려면 x의 자릿수의 합으로 x가 나누어져야 합니다. 예를 들어 18의 자릿수 합은 1+8=9이고, 18은 9로 나누어 떨어지므로 18은 하샤드 수입니다. 자연수 x를 입력받아 x가 하샤드 수인지 아닌지 검사하는 함수, solution을 완성해주세요.

    ~~~ java
    class Solution {
        public boolean solution(int x) {
            boolean answer = true;
            int sum = 0;

            String aaa = Integer.toString(x); // int형을 문자열로 변환

            char arr[] = aaa.toCharArray();

            for (int i = 0; i < arr.length; i++) {
                sum += Integer.valueOf(String.valueOf(arr[i]));
            }

            if (x % sum != 0)
                answer = false;

            return answer;
        }
    }
    ~~~
    ~~~ java
    class Solution {
    	public boolean solution(int x) {
            String[] temp = String.valueOf(num).split("");

            int sum = 0;
            for (String s : temp) {
                sum += Integer.parseInt(s);
            }

            if (num % sum == 0) {
                return true;
            } else {
                return false;
            }
        }
    }
    ~~~

***












