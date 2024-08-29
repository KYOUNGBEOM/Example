# 병합정렬
```java
public class MergeSort {

    // 메인 함수
    public static void main(String[] args) {
        int[] array = {38, 27, 43, 3, 9, 82, 10};
        
        System.out.println("원본 배열:");
        printArray(array);
        
        mergeSort(array, 0, array.length - 1);
        
        System.out.println("\n정렬된 배열:");
        printArray(array);
    }

    // 병합 정렬 함수
    public static void mergeSort(int[] array, int left, int right) {
        if (left < right) {
            int mid = (left + right) / 2;
            
            // 왼쪽 절반 정렬
            mergeSort(array, left, mid);
            // 오른쪽 절반 정렬
            mergeSort(array, mid + 1, right);
            // 병합
            merge(array, left, mid, right);
        }
    }

    // 배열 병합 함수
    public static void merge(int[] array, int left, int mid, int right) {
        // 두 서브 배열 크기 계산
        int n1 = mid - left + 1;
        int n2 = right - mid;
        
        // 임시 배열 생성
        int[] L = new int[n1];
        int[] R = new int[n2];
        
        // 서브 배열 복사
        System.arraycopy(array, left, L, 0, n1);
        System.arraycopy(array, mid + 1, R, 0, n2);
        
        // 병합
        int i = 0; // L 배열 인덱스
        int j = 0; // R 배열 인덱스
        int k = left; // 원본 배열 인덱스
        
        while (i < n1 && j < n2) {
            if (L[i] <= R[j]) {
                array[k++] = L[i++];
            } else {
                array[k++] = R[j++];
            }
        }
        
        // L 배열의 남은 요소 복사
        while (i < n1) {
            array[k++] = L[i++];
        }
        
        // R 배열의 남은 요소 복사
        while (j < n2) {
            array[k++] = R[j++];
        }
    }

    // 배열 출력 함수
    public static void printArray(int[] array) {
        for (int value : array) {
            System.out.print(value + " ");
        }
        System.out.println();
    }
}
```

```java
package 박경범;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class A055_SWEA_14229_백만개의정수정렬 {
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

		String[] input = br.readLine().split(" ");
		int[] A = new int[input.length];


		for(int i = 0; i < input.length; i++) {
			A[i] = Integer.parseInt(input[i]);
		}

		mergeSort(A, 0, A.length-1);

		System.out.println(A[500000]);

	}

	public static void mergeSort(int[] arr, int left, int right) {
		if(left < right) {
			int mid = (left + right) / 2;

			mergeSort(arr, left, mid);
			mergeSort(arr, mid+1, right);
			merge(arr, left, mid, right);

		}
	}

	public static void merge(int[] arr, int left, int mid, int right) {
		int N1 = mid-left+1;
		int N2 = right-mid;

		int[] L = new int[N1];
		int[] R = new int[N2];
		
		System.arraycopy(arr, left, L, 0, N1);
		System.arraycopy(arr, mid+1, R, 0, N2);

		int l = 0;
		int r = 0;
		int index = left;

		while(l < N1 && r < N2) {
			if(L[l] <= R[r]) {
				arr[index++] = L[l++];
			} else {
				arr[index++] = R[r++];
			}
		}

		while(l < N1) {
			arr[index++] = L[l++];
		}

		while(r < N2) {
			arr[index++] = R[r++];
		}
	}
}
```
