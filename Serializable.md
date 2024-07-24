```java
import java.io.Serializable;

// 객체 직렬화 가능하도록 _________ 인터페이스를 구현한다.
public class Car implements Serializable {
	// 1: 국가 2: 제작사 3: 차량구분 4: 차종 5: 세부차종 6: 차체형상 7: 안정장치
	// 8: 배기량 9: 확인란 10: 제작년도 11: 공장위치 12~17: 제작일련번호
	private String VIN; // 자동차 등로 번호 17자리
	private String modelName; // 자동차 모델 이름
	private String color; // 자동차 색상
	private int mileage; // 주행거리

	// 기본 생성자
	public Car() {
	}

	// 인자를 받는 생성자
	public Car(String VIN, String modelName, String color, int mileage) {
		this.VIN = VIN;
		this.modelName = modelName;
		this.color = color;
		this.mileage = mileage;
	}

	/**
	 * 자동차 등록번호를 반환한다.
	 * 
	 * @return VIN
	 */
	public String getVIN() {
		return VIN;
	}

	/**
	 * 자동차 등록번호를 저장한다.
	 * 
	 * @param VIN
	 */
	public void setVIN(String VIN) {
		this.VIN = VIN;
	}

	/**
	 * 자동차 모델이름을 반환한다.
	 * 
	 * @return modelName
	 */
	public String getModelName() {
		return modelName;
	}

	/**
	 * 자동차 모델이름을 저장한다.
	 * 
	 * @param modelName
	 */
	public void setModelName(String modelName) {
		this.modelName = modelName;
	}

	/**
	 * 자동자 색상을 반환한다.
	 * 
	 * @return color
	 */
	public String getColor() {
		return color;
	}

	/**
	 * 자동차 색상을 저장한다.
	 * 
	 * @param color
	 */
	public void setColor(String color) {
		this.color = color;
	}

	/**
	 * 자동차 주행거리를 반환한다.
	 * 
	 * @return mileage
	 */
	public int getMileage() {
		return mileage;
	}

	/**
	 * 자동차 주행거리를 저장한다.
	 * 
	 * @param mileage
	 */
	public void setMileage(int mileage) {
		this.mileage = mileage;
	}

	@Override
	public String toString() {
		return "Car [VIN=" + VIN + ", modelName=" + modelName + ", color=" + color + ", mileage=" + mileage + "]";
	}

}
```

```java
public interface ICarManager {
	/**
	 * 자동차를 추가한다.
	 * 
	 * @param car
	 */
	void add(Car car);

	/**
	 * 등록된 자동차 리스트를 반환한다.
	 * 
	 * @return 등록된 전체 자동차 리스트
	 */
	Car[] getList();

	/**
	 * 해당 이름을 포함하고 있는 자동차 리스트를 반환한다.
	 * 
	 * @param modelName : 조회할 모델 명
	 * @return 모델명을 포함한 자동차 리스트
	 * @throws ModelNameNotFoundException
	 */
	Car[] searchByModelName(String modelName) throws ModelNameNotFoundException;

	/**
	 * 등록된 자동차중에 전기차만 반환한다.
	 * 
	 * @return 등록된 전체 전기차 리스트
	 */
	ElectricCar[] getElectricCars();

	/**
	 * 자동차 주행거리의 총합을 반환한다.
	 * 
	 * @return 등록된 자동차 주행거리의 총합
	 */
	int getTotalMileage();
	
	/**
	 * 자동차 리스트를 파일에 저장한다.
	 */
	void saveData();
}
```

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.util.ArrayList;
import java.util.List;

public class CarMangerImpl implements ICarManager {
	// 관리하는 자동차 리스트
	private List<Car> carList = new ArrayList<Car>();

	// 싱글톤 디자인패턴을 위해 유지하는 객체 참조
	// 클래스가 메모리에 로드될 때 객체를 1번만 생성하여 참조를 유지한다.
	private static ICarManager instance = new CarMangerImpl();

	// 외부에서 객체 생성을 하지 못하도록 생성자를 private 으로 선언
	private CarMangerImpl() {
		//객체가 생성될때 파일을 읽어와 저장
		;
	}

	// 내부에서 생성한 객체의 참조를 반환한다.
	public static ICarManager getInstance() {
		return instance;
	}

	// ArrayList는 크기의 제한이 없으므로 저장 성공/실패를 boolean으로 반환하지 않아도 된다.
	@Override
	public void add(Car car) {
		carList.add(car);
	}

	@Override
	public Car[] getList() {
		this.loadData();
		Car[] result = new Car[carList.size()];

		for (int i = 0; i < carList.size(); i++) {
			result[i] = carList.get(i);
		}

		return result;
	}

	// 해당 모델명으로 자동차를 찾지 못했을 경우 에러를 발생시킨다.
	@Override
	public Car[] searchByModelName(String modelName) throws ModelNameNotFoundException {
		List<Car> tempList = new ArrayList<Car>();

		for (Car c : carList) {
			if (c.getModelName().contains(modelName))
				tempList.add(c);
		}

		if (tempList.size() == 0)
			throw new ModelNameNotFoundException(modelName);

		Car[] result = new Car[tempList.size()];
		for (int i = 0; i < tempList.size(); i++) {
			result[i] = tempList.get(i);
		}
		return result;
	}

	@Override
	public ElectricCar[] getElectricCars() {
		List<ElectricCar> tempList = new ArrayList<ElectricCar>();

		for (Car c : carList) {
			if (c instanceof ElectricCar)
				tempList.add((ElectricCar) c);
		}
		ElectricCar[] eList = new ElectricCar[tempList.size()];

		for (int i = 0; i < tempList.size(); i++) {
			eList[i] = tempList.get(i);
		}

		return eList;
	}

	@Override
	public int getTotalMileage() {
		int total = 0;

		for (Car c : carList) {
			total += c.getMileage();
		}

		return total;
	}

	private void loadData() {
		File file = new File("C:\\car.dat");
		if(file.exists()) { // 파일이 존재하면 파일에서 데이터 읽기
			try {
				ObjectInputStream ois = new ObjectInputStream (new FileInputStream(file));
				carList = (List<Car>) ois.readObject();
			} catch(Exception e) {
				System.out.println("파일 불러오기 실패");
				e.printStackTrace();
			}
		}
	}

	@Override
	public void saveData() {
		try(ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("C:\\car.dat"))){
			oos.writeObject(carList);
		} catch(Exception e) {
			System.out.println("파일 저장 실패");
			e.printStackTrace();
		}
	}
}
```

```java
public class CarTest {
	public static void main(String[] args) {
		
//		 car.dat 파일생성
//		Car car1 = new Car("KMHXX00XXXX000000", "쏘나타", "Red", 12000);
//		Car car2 = new Car("KNHXX00XXXX000000", "K5", "White", 35000);
//		Car car3 = new Car("KNHXX00XXXX000001", "K3", "Black", 3000);
//		ElectricCar ecar1 = new ElectricCar("KMHXX00XXXX0000123", "아이오닉", "Gray", 5000, 100);
//
//		ICarManager cm = CarMangerImpl.getInstance();
//
//		cm.add(car1);
//		cm.add(car2);
//		cm.add(car3);
//		cm.add(ecar1);
//
//		cm.saveData();
		
		ICarManager cm = CarMangerImpl.getInstance();
		System.out.println("-------------getList-------------");
		Car[] carList = cm.getList();
		for (Car c : carList)
			System.out.println(c);

	}
}
```

```java
public class ModelNameNotFoundException extends Exception{

	private String modelName;
	
	public ModelNameNotFoundException(String modelName) {
		super(modelName + " 모델명을 포함하는 자동차가 존재하지 않습니다.");
		this.modelName = modelName;
	}
	
	public String getTitle() {
		return this.modelName;
	}
	
}
```
