    instanceof는 메모리적으로 효율이 매우 떨어지기 때문에, 오버라이딩을 사용해서 메모리 효율성을 올려야함, 이것이야말로 진정한 객체지향!

```java
public class Test {
	public static void main(String[] args) {
		Circle c = new Circle(3);
		Rectangle r = new Rectangle(4, 5);
		Triangle t = new Triangle(6, 7);
		
		Printer out = new Printer();
		out.print(c);
		out.print(r);
		out.print(t);
	}
}
```

```java
public class Shape {
	public void getArea() {}
}
```

```java
public class Circle extends Shape {
    private int radius;
    
    public Circle(int radius) {
    	super();
    	if(radius>0) {
    		this.radius = radius;
    	} else {
    		System.out.println("0보다 커야합니다.");
    	}    	
    }

    public void getArea(){
    	System.out.println("Circle의 넓이는 " + (Math.PI*radius*radius));
    }
    
}
```

```java
public class Triangle extends Shape {
    private int w;
    private int h;

    public Triangle(int w, int h) {
    	super();
    	if(w>0 && h>0) {
    		this.w = w;
    		this.h = h;
    	} else {
    		System.out.println("0보다 커야합니다.");
    	}    	
    }      
    
    public void getArea(){
    	System.out.println("Tirangle의 넓이는 " + (w*h));    	
    }

}
```

```java
public class Rectangle extends Shape {
    private int w;
    private int h;

    public Rectangle(int w, int h) {
    	super();
    	if(w>0 && h>0) {
    		this.w = w;
    		this.h = h;
    	} else {
    		System.out.println("0보다 커야합니다.");
    	}    	
    }   
    
    public void getArea(){
    	System.out.println("Rectangle의 넓이는 " + (w*h));
    }

}
```
