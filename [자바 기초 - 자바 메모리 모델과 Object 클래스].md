### [자바 기초 - 자바 메모리 모델과 Object 클래스]



* 자바 메모리 모델 : 메소드 area / call stack / heap
* Object 클래스 : 11가지





#### 1. 자바의 메모리 모델

- 메모리 효율을 위해 메모리를 구분지음



**①  method area**

		-  구성 : 메소드 바이트 코드 / static 변수
		-  static은 프로그램이 종료될 때까지는 유지, 프로그램이 종료되면 소멸.



**② call stack**

- 구성 : 지역변수 / 매개 변수

- 제일 먼저 main이 들어오고(push), 그 안에 main의 지역 변수가 들어옴.

  그 다음으로 프로그램의 다른 함수들이 차곡차곡 들어옴.

- 메소드 실행이 끝나면 들어온 반대 순서로 하나씩 소멸됨 (pop). 

  각 함수의 지역 변수도 메소드가 소멸되면 같이 소멸됨.

- 프로그램이 종료되기까지 main만 남아 있게 됨.

  

**③ heap**

- 구성 : 인스턴스

- 인스턴스만 heap 영역에 들어가고, 인스턴스의 주소를 가리키는 참조 변수는 stack 영역에 있음(main의 지역변수)

- 인스턴스의 소멸은 참조 변수 초기화를 null로 했을 때.

  null로 초기화 되어 관계가 끊어진 인스턴스는 메모리만 차지하고 있는 상태. 

  이것을 정리해주는 역할을 하는 것이 '가비지 컬렉션(gc) (역시 객체)' (JVM 안에 있음)

- null은 곧 gc에게 해당 인스턴스를 처리해도 좋다고 알려주는 표시이기도 함.



#### 2. Object 클래스

1. **finalize 메소드**

   - 역할 :  인스턴스 소멸시 ''자동으로 호출''됨

   - 사용 방법 : finalize 메소드를 오버라이딩한 후, Object 클래스(부모 클래스)에 있는 finalize 메소드의 

     ​					구현 내용을 가져오기 위해 super.finalize(); 를 함께 명시 (이 메소드를 호출하기 위해서)

     ​					해주어야 함. 

      (오버라이딩은 부모 클래스의 같은 이름을 가진 메소드가 사라지고, 새로 구현 내용을 정의한 같은 이름의   자식 클래스 메소드만 남기 때문에 부모 클래스 메소드의 기능도 쓰고 싶다면, super로 부모 메소드 내용을 불러와야함)

     ```
        @Override
        protected void finalize() throws Throwable {		// Object 클래스의 finalize를
        													// 쓰겠다는 의미
           super.finalize();   					// 부모 클래스의 finalize 메소드 기능 호출
           System.out.println("destroyed: " + name);		// 자식 클래스에서 구현할 내용
        }
     
     ```

     

   - 오버라이딩 하는 이유 : 인스턴스가 소멸될 때, finalize 메소드 안에 명시한 명령을 실행해주기 위해.

   - 만약 super.finalize(); 를 호출하지 않으면, 인스턴스 소멸시 finalize 메소드가 자동으로 호출되질 x.

   - 실제로는 쓰일 일이 x (자바 최신 버전에서는 쓰지 않도록 권고함)

   

   

2.  **equals 메소드**

   - 형태 
   
     ```
        public boolean equals(Object obj) {
             return (this == obj);
         }
     ```
     
     
   
     1.  오버라이딩 안했을 때 : 비교할 참조변수 1.equals (비교할 참조변수 2) 
   
        ​										→ 보통 if문과 같이 써서 true, false로 결과 출력됨.
   
        ​										→ 결과는 두 인스턴스의 주소로 비교됨.
   
     2. 오버라이딩 했을 때 :
   
        @Override
   
         접근 제한자 + 데이터 타입(boolean) + equals(Object obj) {
   
        ​							구현 내용
   
         }
   
        ```
      ex.
          @Override
        
          public boolean equals(Object obj) {	// 부모클래스 인스턴스 방(obj)이 만들어짐
        
           if(this.num == ((INum)obj.num)		// 자식으로 형변환 후 접근해야 함
        
        	     return true;
        
           else
        
            	 return false;
          }
        ```
   
   
   
   
   
   - String 클래스의  equals 메소드
   
     - equals 메소드 
   
     ```
     public static void main(String[] args) {
     
       String str1 = new String("So Simple");
     
       String str2 = new String("So Simple");
     
     
     
       // 참조 대상을 비교하는 if ~ else문
     
       if(str1 == str2)
     
        System.out.println("str1, str2 참조 대상 동일하다.");
     
       else
     
        System.out.println("str1, str2 참조 대상 다르다.");
     
     
     
       // 두 인스턴스 내용 비교하는 if ~ else문
     
       if(str1.equals(str2))		// 원래 equals라면 주소값을 비교해서 '다르다'가 출력되어야 함
     							// 답은 true 
     				// Object의 equals를 자식(String 클래스)의 equals로 오버라이딩 했기 때문에
     							// 본인의 목적에 맞는 내용으로, 주소가 아닌 내용값이 비교됨.
     
        System.out.println("str1, str2 내용 동일하다.");
     
       else
     
        System.out.println("str1, str2 내용 다르다.");
     
     }
     ```
   
     
   
     - == 연산자 : Object 클래스가 가진 원래 equals의 의미는 == (주소가 같느냐는) 의미.
   
       → 연산자 == 는 비교하는 두 문자열이 같은 객체인지를 확인하는 것.
     
     
   
   
   
3. **clone 메소드**

   - 의미 : Object 클래스에 정의된 clone 메소드가 호출되면 인스턴스의 복사가 이뤄짐.

     ​          → clone 메소드가 붙은 참조변수의 인스턴스 구조 + 값까지 모두 그대로 복사해서 새로운 주소를        부여한 뒤, 같은 참조형인 새 변수에 넣어주면 똑같이 생긴 서로 다른 주소의 인스턴스를 가리킴

     ```
     class Point implements Cloneable {
     	private int xPos;
     	private int yPos;
     	
     
     	public Point(int x, int y) {
     		xPos = x;
     		yPos = y;
     	}
     	
     	public void showPosition() {
     		System.out.printf("[%d, %d]", xPos, yPos);
     		System.out.println();
     	}
     	
     	@Override
     	public Object clone() throws CloneNotSupportedException {
     		return super.clone();	// clone 메소드를 사용하기 위해 Object에서 불러옴.
     	}
     
     }
     ```

     ```
     public class Clone {
     	public static void main(String[] args) {
     		Point org = new Point(3, 5);
     		Point cpy;
     		
     
     		try {
     			cpy = (Point)org.clone();	// clone을 통한 객체 생성
     			org.showPosition();
     			cpy.showPosition();
     		}
     		catch(CloneNotSupportedException e) {
     			e.printStackTrace();
     		}
     	}
     
     }
     ```

     

   - clone 메소드를 사용하려면 마커 인터페이스인 'Cloneable'을 implements 해야함 (정해진 규칙)

   - cf. 객체 생성 방법 ① new , ② clone 메소드

     ​	→ 차이점 :  clone이 성능 상 더 빠름 / clone은 상속이 엮여 있기 때문에 활용 시 복잡해질 수 있음.

     ​						/ new는 처음 인스턴스를 생성할 때 사용, clone은 복사할 인스턴스가 있어야 함

     ​						/ clone은 게임 개발에서 똑같은 요소를 수십-수백개 쓸 때 사용하고 랜덤으로 돌림.

   - 주의 사항 : clone 메소드 사용할 때 "형변환"

     ![image-20201218002233134](C:\Users\김슬기\AppData\Roaming\Typora\typora-user-images\image-20201218002233134.png)

     ​																or

     ![image-20201218002316732](C:\Users\김슬기\AppData\Roaming\Typora\typora-user-images\image-20201218002316732.png)

   

   

   - Shallow Copy

     - ★ UML 반드시 그릴 수 있어야 함.

     - shallow copy = 얕은 복사

     - 얕은 복사, 깊은 복사는 클래스 안에 "참조형이 들어갔을 때" 생기는 문제임.

     - 의미 : 클래스 안에 객체형(참조형)이 있는데 그것을 clone을 하게 되면, 그 객체형만 clone하고 각 객체형의 객체는 복제하지 x. 객체형들이 생성(new)한 객체들은 가져오지 x. 이런 상태를 Shallow Copy라고 함.

       따라서 이런 상황을 막으려면 객체의 객체들도 다시 clone 해줘야 함.

       (주소를 불러오는 것이 아니라 객체를 따로 생성하는 것 = Deep Copy = 깊은 복사)

       → 이때는 기존 객체의 값의 변화가 복제된 객체의 값까지 변화시킴.

       ​     서로 다른 값을 대입하기 위해서는 깊은 복사를 해줘야 함.

     
   
   - Deep Copy
   
     - ★ UML 반드시 그릴 수 있어야 함.
     - Deep Copy = 깊은 복사

```
@Override

public Object clone() throws CloneNotSupportedException {

  Rectangle copy = (Rectangle)super.clone();		// Shallow Copy처럼 Rectangle의 객체
  													// 주소를 받은 상태
													//  	↓
  copy.upperLeft = (Point)upperLeft.clone();		// Point 객체를 공유하지 않도록 각각
													// 생성해줌. 따라서 위의 Rectangle 객체는
  copy.lowerRight = (Point)lowerRight.clone();		// 새로운 주소를 받게 됨.



  return copy;

}
```

![Shallow, Deep Copy UML](C:\Users\김슬기\Desktop\Shallow, Deep Copy UML.png)



#### 3. 래퍼 클래스

- 종류 : Boolean, Character, Byte, Short, Integer, Long, Float, Double

- 의미 : 기본 자료형 8가지의 값을 인스턴스로 감싸줌. (기본형을 객체로 만듦)

- 장점 : 클래스 안의 많은 메소드 기능들을 쓸 수 있음.

- 특징
  - 생성자에 입력한 값이 출력되는 원리는 "다형성" - Object 클래스의 toString 메소드가 호출되면서 출력됨.
  
  - 참조형이 의미하는 데이터 타입의 '인스턴스 변수 + toString 메소드'를 각각 가지고 있다는 의미.
  
    ?? 맞는지 생각해보기 ( 이 인스턴스 변수는 final 상수임. )
  
  - 기본형에서 쓸 수 있는 연산자를 그대로 쓸 수 있음. 하지만 연산의 결과는 계속 객체가 생성되어 메모리에 저장됨.
  
  - 원래 래퍼 클래스와 다른 형의 메소드로 호출하면, 호출된 메소드 형식으로 값이 출력됨.
  
    → 장점 : 강제 형변환 없이 메소드를 사용할 수 있음.
  
    ```
    public static void main(String[] args) {
    
      Integer num1 = new Integer(29);
    
      System.out.println(num1.intValue());  // int형 값으로 반환
    
      System.out.println(num1.doubleValue());  // double형 값으로 반환
    
    
    
      Double num2 = new Double(3.14);
    
      System.out.println(num2.intValue());  // int형 값으로 반환
    
      System.out.println(num2.doubleValue());  // double형 값으로 반환
    
    }
    ```
  
    
  
    
  
- 기능

  - 박싱 : 데이터 타입에 맞는 상수를 직접 객체에 넣어주는 것(감싸주는 것).

    ​			(기본형 → 참조형)

  - 언박싱 :  래퍼 클래스의 참조변수를 통해 메소드(  xxValue(); )로 입력된 값을 가져오는 것. 

    ​				(참조형 → 기본형)

  - 오토 박싱 : 객체 생성 부분 없이 직접 값을 대입할 수 있도록 해줌. (new 없이)

  - 오토 언박싱 :  .xxValue(); 없이 참조변수만 대입하면 값을 불러옴.

    ```
    public static void main(String[] args) {
    
          Integer iObj = new Integer(10);  // 박싱
    
          Double dObj = new Double(3.14);  // 박싱
    
          . . . . 
    
    
    
          int num1 = iObj.intValue();      // 언박싱
    
          double num2 = dObj.doubleValue();   // 언박싱 
    
          . . . . 
    
    
    
          // 래퍼 인스턴스 값의 증가 방법
    
          iObj = new Integer(iObj.intValue() + 10);
    
          dObj = new Double(dObj.doubleValue() + 1.2);
    
          Integer num1 = 10;			// 오토 박싱과
          num++;						// 오토 언박싱을 동시에 하는 예시
    
    }
    ```

    ```
    class AutoBoxingUnboxing {
    
      public static void main(String[] args) {
    
       Integer iObj = 10;   // 오토 박싱 진행
    
       Double dObj = 3.14;   // 오토 박싱 진행
    
       . . . . 
    
       
    
       int num1 = iObj;   // 오토 언박싱 진행
    
       double num2 = dObj;   // 오토 언박싱 진행
    
       . . . . 
    
      }
    }
    ```

  

  

- Number 클래스 

  - 모든 래퍼 클래스의 조상  =  모든 래퍼 클래스가 상속하는 클래스

  

- 래퍼 클래스의 다양한 static 메소드

  - . valueOf( ); 

    → '오버로딩'이 적용됨. (같은 함수 이름으로 여러 타입의 값이 적용됨)

    → static 메소드임. (클래스 이름으로 접근하고 있음)

    → ex. Integer n1 = Integer.valueOf(5);					// 정수형을 받는 valueOf 메소드

    ​			Integer n1 = Integer.valueOf("1024");		// 문자열을 받는 valueOf 메소드

    → 주의 사항 : 기본형 변수에 받아도 문제없이 컴파일 됨.			=		"언박싱" 때문

    ​						( ex. int n3 = Integer.valueOf("1024"); )				=  많이 사용하는 형식

  - max, min, sum, toBinaryString, toOctalString, toHexString ( ) 등

    

    

- cf. 인터넷 세상은 기본형은 거의 직접 쓰일 일이 없음. 모든 데이터가 String으로 전달되도록 되어 있음. 

  ​	때문에 이런 클래스가 쓰이게 되는 것.





#### 4. BigInteger 클래스 / BigDecimal 클래스

①  BigInteger 클래스

- 용도 : 매우 큰 정수를 표현하기 위한 클래스

  ​			 (long 형의 범위를 넘어서는 숫자 표현 = long형을 넘어가면 연산을 할 수 없기 때문)

- 형태 : BigInteger 변수 이름  =  new BigInteger( long형을 넘는 숫자 );

  ​			→ 변수의 데이터 타입도 담을 숫자가 크기 때문에 BigInteger로 해야 함.

- 메소드 : add(), multiply(), intValueExact() 

  

②  BigDecimal 클래스

- 용도 : 오차 없는 실수 표현을 위한 클래스
- 메소드 : add, subtract, multiply, divide ()

