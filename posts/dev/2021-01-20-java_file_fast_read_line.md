---
title: java 파일 라인수 얻기, 특정라인 빨리 읽기 (빠른 라인처리)
author: macle
date: 2021-01-20 10:30:00 +0800
categories: [개발]
tags: [java,file,data]
---

# java 빠른 라인처리
개발을 하던중 데이터 시스템을 제작할 일이 생겨서 java 로 복잡하지 않은 시스템을 만들기로 하였습니다. 아래와같이 간단한 설계를 진행하였고 ..
 - 데이터는 json object 단위로 파일의 한라인에 입력
 - 병렬 접근이 가능하게 하기위해 파일을 설정한 용량으로 나누어서 생성
 - 각 데이터의 인덱스는 파일경로, 라인위치로 지정함

위 요건들을 충족하기 위해서 빠른 속도로 파일 라인수를 읽고 특정라인을 빨리 읽어오는 부분이 필요하게 되었습니다.

# 소스
예제 소스에서 사용되는 내용은 gradle로 의존성을 추가하고
```gradle
implementation 'com.seomse.commons:seomse-commons:1.2.4'
```

java 소스에서 아래와 같이 사용할 수 있습니다
```java
int count = FileUtil.getLineCount(filePath);
String lineValue = FileUtil.getLine(filePath, 77)
```

# 성능비교
## 성능 비교에 사용한 소스

```java

//java.io 패키지 사용 
try(BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream(file), charSet))){
	 String line;
     while ((line = br.readLine()) != null) {
    	  
     }
}catch(IOException e){
	throw new IORuntimeException(e);
}


//java.nio 패키지 사용
try (Stream<String> lines = Files.lines(Paths.get(path), StandardCharsets.UTF_8)) {
    //noinspection OptionalGetWithoutIsPresent
    return lines.skip(lineIndex).findFirst().get();
}catch(IOException e){
    throw new IORuntimeException(e);
}

```
위와 같은 방식으로 구현하여 각각 성능측정을 해보니 nio 가 약간더 좋은 성능을 보였습니다.

이번에는 byte[] 로 읽어와서 \n 의 건수를 측정하거나. 이부분만 String 으로 변환하는 방법을 사용하였더니
훨씬더 빠른 성능을 보였습니다.

```java

try (
		FileInputStream stream = new FileInputStream(path)
){

	int lastLineIndex = 0;

	boolean isMake = lineIndex == lastLineIndex;

	List<byte[]> byteList = new ArrayList<>();
	int size = 0;

	byte[] buffer = new byte[10240];
	int n;

	while ((n = stream.read(buffer)) > 0) {

		int start = 0;

		for (int i = 0; i < n; i++) {

			if (buffer[i] == '\n') {
				lastLineIndex++;
				if(isMake){

					byte [] add = Arrays.copyOfRange(buffer, start, i);
					size += add.length;
					byteList.add(add);

					return toStringBytesList(byteList, size, cs);
				}
				isMake = lineIndex == lastLineIndex;
				start = i+1;
			}
		}

		if(isMake){
			byte [] add = Arrays.copyOfRange(buffer, start, n);
			size += add.length;
			byteList.add(add);

		}
	}


	if(size == 0){
		return "";
	}
	return toStringBytesList(byteList, size, cs);
}catch(IOException e){
	throw new IORuntimeException(e);
}

```
## 성능비교 소스
```java
import com.seomse.commons.utils.FileUtil;
import com.seomse.commons.utils.time.TimeUtil;

/**
 * 인메모리 검색 분석 엔진에서 
 * 파엘에 저장된 상세 정보를 가져올때 사용하려고 core 기술 개발중 빠른 line 텍스트 성능 테스트
 * @author macle
 */
public class FileReadLineNumberSpeedTest {
    public static void main(String[] args) {


        String testFilePath = "D:\\seomse\\index\\20200201\\index_0.md";
        //200 라인의 파일을 사용
		for (int i = 0; i <200 ; i++) {
            //일치여부 테스트
			if(!FileUtil.getLineNio(testFilePath, i).equals(FileUtil.getLine(testFilePath, i))){
				System.out.println(i);
			}
		}

        long startTime = System.currentTimeMillis();
		for (int i = 0; i <500 ; i++) {
            FileUtil.getLineNio(testFilePath, 50);
		}
        System.out.println("line value Nio 속도: " + TimeUtil.getSecond(System.currentTimeMillis()-startTime));
        startTime = System.currentTimeMillis();
        for (int i = 0; i <500 ; i++) {
            FileUtil.getLine(testFilePath, 50);
        }
        System.out.println("line value core 기술 구현체 속도: " + TimeUtil.getSecond(System.currentTimeMillis()-startTime));

        startTime = System.currentTimeMillis();
        for (int i = 0; i <500 ; i++) {
            FileUtil.getLineCountNio(testFilePath);
        }
        System.out.println("line count  Nio 속도: " + TimeUtil.getSecond(System.currentTimeMillis()-startTime) + " " + FileUtil.getLineCountNio(testFilePath));

        startTime = System.currentTimeMillis();
        for (int i = 0; i <500 ; i++) {
            FileUtil.getLineCount(testFilePath);
		}
        System.out.println("line count core 기술 구현체 속도: " + TimeUtil.getSecond(System.currentTimeMillis()-startTime) +" " + FileUtil.getLineCount(testFilePath));

    }
}

```
# 결과
위소스와 같이 500번의 반복을 해서 실험했을떄 나온 결과는
- line value Nio 속도: 2.557
- line value core 기술 구현체 속도: 0.483
- line count Nio 속도: 11.008 200
- line count core 기술 구현체 속도: 1.56 200

특정라인을 읽을때는 5.3 배정도 라인수를 측정할때 걸리는 속도는 7배 정도의 성능차이를 보였습니다. 정확한 원인에 대한 이유는 알지 못했지만 추측해보자면 nio의 Stream을 활용할 때는 모든 라인을 문자열로 변환하는 시간이 들어가는 것으로 예상됩니다.

라인문자만 체크해보고 필요한 부분만 문자열로 변환하기 때문이 아닐까 추측 하고 있습니다.

파일 크기가 크면 클수록 성능 차이는 심할 것으로 예상됩니다.