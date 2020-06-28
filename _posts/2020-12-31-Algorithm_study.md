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

### 나누어 떨어지는 숫자 배열
- array의 각 element 중 divisor로 나누어 떨어지는 값을 오름차순으로 정렬한 배열을 반환하는 함수, solution을 작성해주세요.
- divisor로 나누어 떨어지는 element가 하나도 없다면 배열에 -1을 담아 반환하세요.
- 제한사항
	- arr은 자연수를 담은 배열입니다.
	- 정수 i, j에 대해 i ≠ j 이면 arr[i] ≠ arr[j] 입니다.
	- divisor는 자연수입니다.
	- array는 길이 1 이상인 배열입니다.

	~~~ java
    public int[] solution(int[] arr, int divisor) {
		int[] answer = new int[arr.length];

		int cnt = 0;
		int cnt1 = 0;

		for (int i = 0; i < arr.length; i++) {
			if (arr[i] % divisor == 0) {
				// System.out.println(arr[i]);
				answer[cnt] = arr[i];
				// System.out.println(answer[cnt]);
				cnt++;
			}

		}
		Arrays.sort(answer);
		if (cnt != 0) {
			int[] res = new int[cnt];
			for (int j = 0; j < answer.length; j++) {
				if (answer[j] != 0) {
					res[cnt1] = answer[j];
					cnt1++;
				}
			}

			return res;
		} else {
			int[] res = new int[1];
			res[0] = -1;
			return res;
		}
	}
    ~~~

***

### 모의고사
- 수포자는 수학을 포기한 사람의 준말입니다.
- 수포자 삼인방은 모의고사에 수학 문제를 전부 찍으려 합니다.
- 수포자는 1번 문제부터 마지막 문제까지 다음과 같이 찍습니다.
	- 1번 수포자가 찍는 방식: 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...
	- 2번 수포자가 찍는 방식: 2, 1, 2, 3, 2, 4, 2, 5, 2, 1, 2, 3, 2, 4, 2, 5, ...
	- 3번 수포자가 찍는 방식: 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, ...
- 제한 조건
	- 시험은 최대 10,000 문제로 구성되어있습니다.
	- 문제의 정답은 1, 2, 3, 4, 5중 하나입니다.
	- 가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.

- 1번 문제부터 마지막 문제까지의 정답이 순서대로 들은 배열 answers가 주어졌을 때, 가장 많은 문제를 맞힌 사람이 누구인지 배열에 담아 return 하도록 solution 함수를 작성해주세요.

	~~~ java
    public int[] solution(int[] answers) {
		int[] answer = {};
		int[] person1 = { 1, 2, 3, 4, 5 }; // 이만큼씩 반복
		int[] person2 = { 2, 1, 2, 3, 2, 4, 2, 5 };
		int[] person3 = { 3, 3, 1, 1, 2, 2, 4, 4, 5, 5 };
		int answer1 = 0, answer2 = 0, answer3 = 0;

		for (int i = 0; i < answers.length; i++) {
			if (person1[i % person1.length] == answers[i])
				answer1++;
			if (person2[i % person2.length] == answers[i])
				answer2++;
			if (person3[i % person3.length] == answers[i])
				answer3++;
		}
		int max = Math.max(Math.max(answer1, answer2), answer3); // max값 구하기
		ArrayList<Integer> list = new ArrayList<Integer>();
		if (max == answer1)
			list.add(1); // max값이랑 같으면 넣는다.
		if (max == answer2)
			list.add(2);
		if (max == answer3)
			list.add(3);

		answer = new int[list.size()];

		for (int i = 0; i < answer.length; i++) {
			answer[i] = list.get(i);
		}

		return answer;
	}
    ~~~

***

### 완주하지 못한 선수
- 수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.
- 마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때, 완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.
- 제한사항
	- 마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
	- completion의 길이는 participant의 길이보다 1 작습니다.
	- 참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
	- 참가자 중에는 동명이인이 있을 수 있습니다.

	~~~ java
    public String solution(String[] participant, String[] completion) {
		String answer = "";

		for (int i = 0; i < completion.length; i++) {
			for (int j = 0; j < participant.length; j++) {
				if (completion[i].equals(participant[j])) {

					participant[j] = "";
					completion[i] = "";
				}
			}
		}
		Arrays.sort(participant);
		answer = participant[participant.length - 1];

		return answer;
	}
    ~~~

    ~~~ java
    public String solution1(String[] participant, String[] completion) {
		String answer = "";
		int val = 0;

		Map<String, Integer> hm = new HashMap<>();

		for (String part : participant) {
			if (hm.get(part) == null)
				hm.put(part, 1);
			else {
				val = hm.get(part) + 1;
				hm.put(part, val);
			}
		}

		for (String comp : completion) {
			val = hm.get(comp) - 1;
			hm.put(comp, val);
		}

		for (String key : hm.keySet()) {
			if (hm.get(key) == 1)
				answer = key;
		}

		return answer;
	}
    ~~~

    ~~~ java
    public String solution2(String[] participant, String[] completion) {
		Arrays.sort(participant);
		Arrays.sort(completion);
		int i;
		for (i = 0; i < completion.length; i++) {

			if (!participant[i].equals(completion[i])) {
				return participant[i];
			}
		}
		return participant[i];
	}
    ~~~

***

### 2016년
- 2016년 1월 1일은 금요일입니다. 2016년 a월 b일은 무슨 요일일까요?
- 두 수 a ,b를 입력받아 2016년 a월 b일이 무슨 요일인지 리턴하는 함수, solution을 완성하세요.
- 요일의 이름은 일요일부터 토요일까지 각각 SUN,MON,TUE,WED,THU,FRI,SAT 입니다.
- 예를 들어 a=5, b=24라면 5월 24일은 화요일이므로 문자열 TUE를 반환하세요.
- 제한 조건
	- 2016년은 윤년입니다.
	- 2016년 a월 b일은 실제로 있는 날입니다. (13월 26일이나 2월 45일같은 날짜는 주어지지 않습니다)

	~~~ java
    public String solution(int a, int b) {
		String answer = "";

		int month_days[] = { 31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };
		int count = 0;

		if (a == 1) {
			count = b;
		} else if (a == 2) {
			count = month_days[0] + b;
		} else {
			for (int i = 0; i <= (a - 2); i++) {
				count += month_days[i];
			}
			count = count + b;
		}

		if (count % 7 == 0) {
			answer = "THU";
		} else if (count % 7 == 1) {
			answer = "FRI";
		} else if (count % 7 == 2) {
			answer = "SAT";
		} else if (count % 7 == 3) {
			answer = "SUN";
		} else if (count % 7 == 4) {
			answer = "MON";
		} else if (count % 7 == 5) {
			answer = "TUE";
		} else if (count % 7 == 6) {
			answer = "WED";
		}
		return answer;
	}
    ~~~

    ~~~ java
    public String solution1(int a, int b) {
	      String answer = "";

	      int[] c = {31,29,31,30,31,30,31,31,30,31,30,31};
	      String[] MM ={"FRI","SAT","SUN","MON","TUE","WED","THU"};
	      int Adate = 0;
	      for(int i = 0 ; i< a-1; i++){
	          Adate += c[i];
	      }
	      Adate += b-1;
	      answer = MM[Adate %7];

	      return answer;
	  }
    ~~~

***

### 소수 찾기
- 1부터 입력받은 숫자 n 사이에 있는 소수의 개수를 반환하는 함수, solution을 만들어 보세요.
- 소수는 1과 자기 자신으로만 나누어지는 수를 의미합니다.(1은 소수가 아닙니다.)
- 제한 조건
	- n은 2이상 1000000이하의 자연수입니다.

	~~~ java
    public int solution(int n) {
          int answer = 0;

          int[] number = new int[n+1];

          //2부터 n까지의 수를 배열에 넣는다.
          for(int i=2; i<=n; i++) {
              number[i] = i;
          }

          //2부터 시작해서 그의 배수들을 0으로 만든다.
          //후에 0이면 넘어가고 아니면 그의 배수들을 다시 0으로 만든다.
          for(int i=2; i<=n; i++) {
              if(number[i]==0) continue;

              for(int j= 2*i; j<=n; j += i) {
                  number[j] = 0;
              }
          }

          //배열에서 0이 아닌 것들의 개수를 세준다.
          for(int i=0; i<number.length; i++) {
              if(number[i]!=0) {
                  answer++;
              }
          }

          return answer;
      }
    ~~~

***

### 예산
- S사에서는 각 부서에 필요한 물품을 지원해 주기 위해 부서별로 물품을 구매하는데 필요한 금액을 조사했습니다. 그러나, 전체 예산이 정해져 있기 때문에 모든 부서의 물품을 구매해 줄 수는 없습니다. 그래서 최대한 많은 부서의 물품을 구매해 줄 수 있도록 하려고 합니다.

- 물품을 구매해 줄 때는 각 부서가 신청한 금액만큼을 모두 지원해 줘야 합니다. 예를 들어 1,000원을 신청한 부서에는 정확히 1,000원을 지원해야 하며, 1,000원보다 적은 금액을 지원해 줄 수는 없습니다.

- 부서별로 신청한 금액이 들어있는 배열 d와 예산 budget이 매개변수로 주어질 때, 최대 몇 개의 부서에 물품을 지원할 수 있는지 return 하도록 solution 함수를 완성해주세요.

![image](https://user-images.githubusercontent.com/52989294/85914668-3cdfa780-b87b-11ea-8258-d0be7e531778.png)

- 입출력 예 #1
	- 각 부서에서 [1원, 3원, 2원, 5원, 4원]만큼의 금액을 신청했습니다. 만약에, 1원, 2원, 4원을 신청한 부서의 물품을 구매해주면 예산 9원에서 7원이 소비되어 2원이 남습니다. 항상 정확히 신청한 금액만큼 지원해 줘야 하므로 남은 2원으로 나머지 부서를 지원해 주지 않습니다. 위 방법 외에 3개 부서를 지원해 줄 방법들은 다음과 같습니다.

	- 1원, 2원, 3원을 신청한 부서의 물품을 구매해주려면 6원이 필요합니다.
    - 1원, 2원, 5원을 신청한 부서의 물품을 구매해주려면 8원이 필요합니다.
    - 1원, 3원, 4원을 신청한 부서의 물품을 구매해주려면 8원이 필요합니다.
    - 1원, 3원, 5원을 신청한 부서의 물품을 구매해주려면 9원이 필요합니다.
    - 3개 부서보다 더 많은 부서의 물품을 구매해 줄 수는 없으므로 최대 3개 부서의 물품을 구매해 줄 수 있습니다.

- 입출력 예 #2
	- 모든 부서의 물품을 구매해주면 10원이 됩니다. 따라서 최대 4개 부서의 물품을 구매해 줄 수 있습니다.

    ~~~ java
    public int solution(int[] d, int budget) {
            int answer = 0;
            int result = 0;
            Arrays.sort(d);
            for (int i = 0; i < d.length; i++) {
                result += d[i];
                if (result > budget) {
                    answer = i;
                    break;
                }
            }
            if (result <= budget) {
                answer = d.length;
            }
            return answer;
        }
    ~~~

    ~~~ java
    public int solution1(int[] d, int budget) {
            int answer = 0;
            Arrays.sort(d);
            for (int i = 0; i < d.length; i++) {
                budget -= d[i];
                if (budget < 0)
                    break;
                answer++;
            }
            return answer;
        }
    ~~~

    ~~~ java
    public int solution2(int[] d, int budget) {
            int answer = 0;
            int[] dept = Arrays.copyOf(d, d.length);
            Arrays.sort(dept);
            int sum = 0;
            for (int inx=0; inx<dept.length; inx++) {
                sum += dept[inx];
                if (sum<=budget) {
                    answer++;
                }
            }
            return answer;
        }
    ~~~

***

### 시저암호
- 어떤 문장의 각 알파벳을 일정한 거리만큼 밀어서 다른 알파벳으로 바꾸는 암호화 방식을 시저 암호라고 합니다. 예를 들어 AB는 1만큼 밀면 BC가 되고, 3만큼 밀면 DE가 됩니다. z는 1만큼 밀면 a가 됩니다. 문자열 s와 거리 n을 입력받아 s를 n만큼 민 암호문을 만드는 함수, solution을 완성해 보세요.

	![image](https://user-images.githubusercontent.com/52989294/85914731-c3948480-b87b-11ea-8ec2-251325ec8f80.png)

    ~~~ java
    public static String solution(String s, int n) {
            String answer = "";
            int len = s.length();
            char alpha;
            char start;
            for (int i = 0; i < len; i++) {
                alpha = s.charAt(i);
                if (alpha != ' ') {
                    start = Character.isLowerCase(alpha) ? 'a' : 'A';
                    alpha = (char) (start + (alpha + n - start) % 26);
                }
                answer += alpha;
            }
            return answer;
        }
    ~~~

***

### 체육복
- 점심시간에 도둑이 들어, 일부 학생이 체육복을 도난당했습니다. 다행히 여벌 체육복이 있는 학생이 이들에게 체육복을 빌려주려 합니다.
- 학생들의 번호는 체격 순으로 매겨져 있어, 바로 앞번호의 학생이나 바로 뒷번호의 학생에게만 체육복을 빌려줄 수 있습니다.
- 예를 들어, 4번 학생은 3번 학생이나 5번 학생에게만 체육복을 빌려줄 수 있습니다. 체육복이 없으면 수업을 들을 수 없기 때문에 체육복을 적절히 빌려 최대한 많은 학생이 체육수업을 들어야 합니다.
- 전체 학생의 수 n, 체육복을 도난당한 학생들의 번호가 담긴 배열 lost, 여벌의 체육복을 가져온 학생들의 번호가 담긴 배열 reserve가 매개변수로 주어질 때, 체육수업을 들을 수 있는 학생의 최댓값을 return 하도록 solution 함수를 작성해주세요.

	![image](https://user-images.githubusercontent.com/52989294/85916145-c47fe300-b888-11ea-805d-5b200e0808ba.png)

	- 예제 #1
	 	- 1번 학생이 2번 학생에게 체육복을 빌려주고, 3번 학생이나 5번 학생이 4번 학생에게 체육복을 빌려주면 학생 5명이 체육수업을 들을 수 있습니다.
	- 예제 #2
	 	- 3번 학생이 2번 학생이나 4번 학생에게 체육복을 빌려주면 학생 4명이 체육수업을 들을 수 있습니다.

    ~~~ java
    public int solution(int n, int[] lost, int[] reserve) {
            int answer = n - lost.length; // 온전히 체육복 있는 사람

            // 체육복을 잃어버렸지만 가지고 있는 놈
            for (int i = 0; i < reserve.length; ++i) {
                for (int j = 0; j < lost.length; ++j) {
                    // 둘다 -10 이면 넘어가고
                    if (lost[j] == -10 && reserve[i] == -10)
                        continue;
                    // 체육복 잃어버렸는데 여분 있는 놈들 answer++
                    if (lost[j] == reserve[i]) {
                        lost[j] = -10;
                        reserve[i] = -10;
                        answer++;
                    }
                }
            }

            for (int i = 0; i < lost.length; ++i) {
                for (int j = 0; j < reserve.length; ++j) {
                    if (lost[i] == -10 || reserve[j] == -10)
                        continue;
                    // 체육복 안가져왔는데 앞뒤친구중에 빌려줄 수 있는 사람 있으면
                    if (Math.abs(lost[i] - reserve[j]) == 1) {
                        answer++;
                        reserve[j] = -10;
                        break;
                    }
                }
            }
            return answer;
        }
    ~~~

***

### 피보나치 수
- 피보나치 수는 F(0) = 0, F(1) = 1일 때, 1 이상의 n에 대하여 F(n) = F(n-1) + F(n-2) 가 적용되는 수 입니다.
- 예를들어
	- F(2) = F(0) + F(1) = 0 + 1 = 1
	- F(3) = F(1) + F(2) = 1 + 1 = 2
	- F(4) = F(2) + F(3) = 1 + 2 = 3
	- F(5) = F(3) + F(4) = 2 + 3 = 5
- 2 이상의 n이 입력되었을 때, n번째 피보나치 수를 1234567으로 나눈 나머지를 리턴하는 함수, solution을 완성해 주세요.

    ~~~ java
    public int solution(int n) {
            int answer = 0;

            if (n == 2)
                return 1;

            int n1 = 1, n2 = 1, sum = 0;
            for (int i = 3; i <= n; i++) {
                sum = n1 + n2;
                sum %= 1234567;
                n1 = n2;
                n2 = sum;
            }
            answer = sum;
            return answer;
        }
    ~~~

***
