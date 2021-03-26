---
title: seomse-jdbc 개발 배경과 활용법
author: macle
date: 2021-03-25 21:30:00 +0800
categories: [project,java]
tags: [java,jdbc]
---

# 개요
- java 진영의 jdbc 는 jpa, Hibernate, mybatis 등 다양하게 쓸 수 있는 방법 많습니다.
- 관련 라이브러리를 개발한 시기는 대략 2013년 쯤으로 기억하는데 이때에는 mybatis 가 유행하던 시기로 기억합니다.
  - 정리는 최근에 했지만 개발은 저쯤에 했습니다.
- 수백만 단위의 데이터를 불러와서 수억건의 row 를 저장하는 일들이 있었는데 프레임웍에서 동작하는것보다 low level 까지 들어가서 조작하고 싶어서 제작 하였습니다.
- 제작할 당시에는 Hibernate 가 있다는걸 몰랐습니다.
- 제가 만든거라 그런지 저는 개인적으로 아직도 잘 사용하고 있고 팀 섬세한사람들에는 같이 사용하는 사람들이 있습니다.

# 그당시 상황
- 항상 사용되는 2천만 가량의 row 데이터가 있고, 이 row 데이터를 기반으로 매일 수백만건의 문서를 분석해야 하는 일이 있었습니다.
- 당시에는 분석결과 또한 오라클 엑사데이터에 구성하길 원하는 금융권이 많아서 대량 select 도중 데이터를 받는기능, 대량 insert 관련된 부분이 필요한 시점이었습니다.
- 그래서 2천만 가량의 row 는 싱글턴으로 메모리 인덱스 구조를 생성하여 미리 올려놓고 활용하는 형태로 개발을 하였는데
- 복잡한 쿼리가 필요 없을때가 많았고 불러온 이후에 java 객체로 변환하는 과정이 귀찮고, 단순쿼리를 짜는것도 귀찮아서 개발 하였습니다.

- 또 당시는 dump, link 권한이 없는 상태에서 db 데이터를 이관시키는 일
- oracle -> mysql, mysql -> oracle, oracle -> mssql 등으로 서로 다른 db 시스템으로 이관시키는 일등이 많이 생기는 시기였는데 이러한 경우에 중간에 jdbc를 이용해서 프로그램 적으로 편하게 옮길 필요가 있었습니다.


# 이러한 상황에서 사용
- DB를 설계하기전에 아래처럼 도메인 헤드별 타입을 정의하고, 용어사전을 만들어서 사용하는 경우가 많았는데 당시에는 이러한 규칙이 잘 지켜지지 않으면 데이터 품질평가 점수가 좋지 않았습니다.
  ![도메인 헤드](https://raw.githubusercontent.com/macle86/macle86.github.io/master/img/rdb/domain_heag.JPG)

  ![용어사전](https://raw.githubusercontent.com/macle86/macle86.github.io/master/img/rdb/rdb_dic.JPG)

- 위 규칙을 적용한 테이블 형태
  ![테이블](https://raw.githubusercontent.com/macle86/macle86.github.io/master/img/rdb/table_sample.JPG)

- 위 그림을 보면 항상 컬럼뒤에 도메인 헤드가 붙는 설계형태이고 아무런 도메인 헤드가 붙지않으면 varchar, char 형태의 컬럼입니다.
- 이러한 설계 구조에서 쉽게 사용하고자하여 개발하게 되었습니다.
- java 객체에 어노테이션으로 컬럼명과 테이블을 지정하는 형식, vo형 객체를 쉽게 생성하는 사용형식, List<Map<String,String> 형태의 결과를 쉽게 얻는 형식등 다양한 방법을 쉽고 편하게 쓰기 위해 만들었고 많은 util성 함수를 제공합니다.

# 사용법
## 도메인 헤더에 따른 데이터 형 설정
아래와 같은 java 형 기본형을 매핑 해주고 각 기호는 USER_ID 의 ID 처럼 뒤에 붙는 값 입니다.

- \<entry key="application.jdbc.naming.string"\>ID,VAL,YMD,TP,NM,CD,FG,DS,YMDHM,HM,ST\</entry\>
- \<entry key="application.jdbc.naming.double"\>PRC,RT,SC,VOL,PCT\</entry\>
- \<entry key="application.jdbc.naming.integer"\>NB\</entry\>
- \<entry key="application.jdbc.naming.long"\>CNT\</entry\>
- \<entry key="application.jdbc.naming.datetime"\>DT,TS\</entry\>
- \<entry key="application.jdbc.naming.seq"\>string,double,long,integer,datetime\</entry\>
- \<entry key="application.jdbc.naming.default"\>string\</entry\>


## 객체 생성을 편하게 하는 도구
```java

package com.seomse.jdbc.example.naming;

import com.seomse.jdbc.connection.ApplicationConnectionPool;
import com.seomse.jdbc.naming.JdbcNaming;

/**
 * @author macle
 */
public class NamingObjectMake {

	public static void main(String [] args){
		String tableName = "T_STOCK_ITEM";
		System.out.println("@Table(name=\"" +  tableName+ "\")\n");
		System.out.println(JdbcNaming.makeObjectValue(tableName));

	}
}
```
위와같은 형태의 코드를 사용하면

```java

@PrimaryKey(seq = 1)
private String ITEM_CD;
private String ITEM_FULL_CD;
private String ITEM_NM;
private String ITEM_EN_NM;
private String MARKET_CD = "KOSPI"  ;
private String CATEGORY_NM;
private String WICS_NM;
private String SUMMARY_DS;
@DateTime
private Long DELISTING_DT;
private String TRADE_FG = "Y"  ;
private String LISTING_YMD;
private Long LISTING_CNT;
@DateTime
private Long REG_DT ;
@DateTime
private Long UPT_DT ;

```
위와같은 형태로 console에 생성되는데 필요한 컬럼만 복사하여 객체로 만들어서 사용하거나

```java
package com.seomse.jdbc.example.objects;

import com.seomse.jdbc.connection.ApplicationConnectionPool;
import com.seomse.jdbc.objects.JdbcObjects;

/**
 * @author macle
 */
public class ObjectMake {

  public static void main(String[] args) {
    //noinspection ResultOfMethodCallIgnored
    String tableName = "T_STOCK_ITEM";
    System.out.println(JdbcObjects.makeObjectValue(tableName));

  }
}


```
위의 예제로
```java
@PrimaryKey(seq = 1)
@Column(name = "ITEM_CD")

@Column(name = "ITEM_FULL_CD")

@Column(name = "ITEM_NM")

@Column(name = "ITEM_EN_NM")

@Column(name = "MARKET_CD")

@Column(name = "CATEGORY_NM")

@Column(name = "WICS_NM")

@Column(name = "SUMMARY_DS")

@DateTime
@Column(name = "DELISTING_DT")

@FlagBoolean
@Column(name = "TRADE_FG")

@Column(name = "LISTING_YMD")

@Column(name = "LISTING_CNT")

@DateTime
@Column(name = "REG_DT")

@DateTime
@Column(name = "UPT_DT")


```
위와 같이 어노테이션을 생성하여
```java
@Table(name="B_STOCK_ITEM")
public class StockItem {
  @PrimaryKey(seq = 1)
  @Column(name = "ITEM_CD")
  private String code;
  @Column(name = "ITEM_NM")
  private String name;
  @Column(name = "ITEM_EN_NM")
  private String englishName;
  @Column(name = "MARKET_TP")
  private MarketType marketType = MarketType.KOSPI;
}
```
위 형태의 객체를 만들어 사용하는 형태입니다.

그박에도 com.seomse.jdbc.JdbcQuery 클래스에서
- https://github.com/seomse/seomse-jdbc/blob/master/src/main/java/com/seomse/jdbc/JdbcQuery.java
- public static List<Map<String, String>> getMapStringList(String sql);
- public static String getResultOne(String sql)
  과 같은 다양한 메소드를 지원하니 오픈소스 이므로 많은필요한 부분의 내용을 확인 할 수 있습니다.

또한 DB시간과 시퀀스를 가져오는 부분은 com.seomse.jdbc.Database 에서 제공합니다.
- public static String nextVal(String sequenceName)
- public static long getDateTime()

## 데이터 이관도구
- 데이터 이관 방법은 2가지 형태로 지원합니다
  - 두 db시스템의 connection 을 이용한방식
  - 파일로 내린후 파일을 올리는방식 (json)

- connection 을 이용한 데이터 복사방식
```java

package com.seomse.jdbc.example.admin;

import com.seomse.jdbc.admin.RowDataInOut;
import com.seomse.jdbc.connection.ConnectionFactory;

import java.sql.Connection;

/**
 * @author macle
 */
public class RowDataCopy {
  public static void main(String[] args) {
    try{

      Connection selectConn = ConnectionFactory.newConnection("oracle", "jdbc:oracle:thin:@127.0.0.1:1521:orcl", "select", "select");
      Connection insertConn = ConnectionFactory.newConnection("oracle", "jdbc:oracle:thin:@127.0.0.1:1521:orcl", "insert", "insert");

      String [] tables =
        {"TABLE_NAME"}
        ;
      RowDataInOut rowDataInOut = new RowDataInOut();

      rowDataInOut.tableCopy(selectConn, insertConn,tables);


    }catch(Exception e){
      e.printStackTrace();
    }
  }
}


```

- data file out
```java

package com.seomse.jdbc.example.admin;

import com.seomse.jdbc.admin.RowDataInOut;

/**
 * @author macle
 */
public class RowDataOut {
  public static void main(String[] args) {
    RowDataInOut dataInOut = new RowDataInOut();
    dataInOut.dataOut();
  }

}
```

- data file in
```java

package com.seomse.jdbc.example.admin;

import com.seomse.jdbc.admin.RowDataInOut;

/**
 * @author macle
 */
public class RowDataIn {
  public static void main(String[] args) {
    RowDataInOut dataInOut = new RowDataInOut();
    dataInOut.dataIn();

  }
}

```

# gradle, etc
- 최신버전은 https://github.com/seomse/seomse-jdbc 에서 확인바랍니다 아래는 글 작성 당시의 버전 입니다.
- implementation 'com.seomse.jdbc:seomse-jdbc:0.9.8'
- etc
  - https://mvnrepository.com/artifact/com.seomse.jdbc/seomse-jdbc/0.9.8
