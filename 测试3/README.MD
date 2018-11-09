# 实验3：创建分区表
## 实验目的：
掌握分区表的创建方法，掌握各种分区方式的使用场景。
## 实验内容：
- 本实验使用3个表空间：USERS,USERS02,USERS03。在表空间中创建两张表：订单表(orders)与订单详表(order_details)。
- 使用**你自己的账号创建本实验的表**，表创建在上述3个分区，自定义分区策略。
- 你需要使用system用户给你自己的账号分配上述分区的使用权限。你需要使用system用户给你的用户分配可以查询执行计划的权限。
- 表创建成功后，插入数据，数据能并平均分布到各个分区。每个表的数据都应该大于1万行，对表进行联合查询。
- 写出插入数据的语句和查询数据的语句，并分析语句的执行计划。
- 进行分区与不分区的对比实验。
## 创建orders表的语句
```
CREATE TABLE orders 
(
 order_id NUMBER(10, 0) NOT NULL 
 , customer_name VARCHAR2(40 BYTE) NOT NULL 
 , customer_tel VARCHAR2(40 BYTE) NOT NULL 
 , order_date DATE NOT NULL 
 , employee_id NUMBER(6, 0) NOT NULL 
 , discount NUMBER(8, 2) DEFAULT 0 
 , trade_receivable NUMBER(8, 2) DEFAULT 0 
) 
TABLESPACE USERS 
PCTFREE 10 INITRANS 1 
STORAGE (   BUFFER_POOL DEFAULT ) 
NOCOMPRESS NOPARALLEL 
PARTITION BY RANGE (order_date) 
(
 PARTITION PARTITION_BEFORE_2016 VALUES LESS THAN (
 TO_DATE(' 2016-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 
 'NLS_CALENDAR=GREGORIAN')) 
 NOLOGGING 
 TABLESPACE USERS 
 PCTFREE 10 
 INITRANS 1 
 STORAGE 
( 
 INITIAL 8388608 
 NEXT 1048576 
 MINEXTENTS 1 
 MAXEXTENTS UNLIMITED 
 BUFFER_POOL DEFAULT 
) 
NOCOMPRESS NO INMEMORY  
, PARTITION PARTITION_BEFORE_2017 VALUES LESS THAN (
TO_DATE(' 2017-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 
'NLS_CALENDAR=GREGORIAN')) 
NOLOGGING 
TABLESPACE USERS02 
PCTFREE 10 
 INITRANS 1 
 STORAGE 
( 
 INITIAL 8388608 
 NEXT 1048576 
 MINEXTENTS 1 
 MAXEXTENTS UNLIMITED 
 BUFFER_POOL DEFAULT 
) 
NOCOMPRESS NO INMEMORY  
, PARTITION PARTITION_BEFORE_2018 VALUES LESS THAN (
TO_DATE(' 2018-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 
'NLS_CALENDAR=GREGORIAN')) 
NOLOGGING 
TABLESPACE USERS03
);
```
## 创建order_details表:
```
 CREATE TABLE order_details
(
id NUMBER(10, 0) NOT NULL
, order_id NUMBER(10, 0) NOT NULL
, product_id VARCHAR2(40 BYTE) NOT NULL
, product_num NUMBER(8, 2) NOT NULL
, product_price NUMBER(8, 2) NOT NULL
, CONSTRAINT order_details_fk1 FOREIGN KEY  (order_id)
REFERENCES orders  (order_id)
ENABLE
)
TABLESPACE USERS
PCTFREE 10 INITRANS 1
STORAGE (   BUFFER_POOL DEFAULT )
NOCOMPRESS NOPARALLEL
PARTITION BY REFERENCE (order_details_fk1)
(
PARTITION PARTITION_BEFORE_2016
NOLOGGING
TABLESPACE USERS
PCTFREE 10
 INITRANS 1
 STORAGE
(
 INITIAL 8388608
 NEXT 1048576
 MINEXTENTS 1
 MAXEXTENTS UNLIMITED
 BUFFER_POOL DEFAULT
)
NOCOMPRESS NO INMEMORY,
PARTITION PARTITION_BEFORE_2017
NOLOGGING
TABLESPACE USERS02
PCTFREE 10
 INITRANS 1
 STORAGE
(
 INITIAL 8388608
 NEXT 1048576
 MINEXTENTS 1
 MAXEXTENTS UNLIMITED
 BUFFER_POOL DEFAULT
)
NOCOMPRESS NO INMEMORY,
PARTITION PARTITION_BEFORE_2018
NOLOGGING
TABLESPACE USERS03
PCTFREE 10
 INITRANS 1
 STORAGE
(
 INITIAL 8388608
 NEXT 1048576
 MINEXTENTS 1
 MAXEXTENTS UNLIMITED
 BUFFER_POOL DEFAULT
)
NOCOMPRESS NO INMEMORY
);
```
## 插入数据
```
declare
  dt date;
  V_EMPLOYEE_ID NUMBER(6);
  v_order_id number(10);
  v_name varchar2(100);
  v_tel varchar2(100);
  v_product_id varchar2(100);
begin
  for i in 1..10000
  loop
    if i mod 2 =0 then
      dt:=to_date('2016-1-1','yyyy-mm-dd')+(i mod 60);
      v_product_id:= '2';
    else if i mod 3=0 then
      dt:=to_date('2017-6-1','yyyy-mm-dd')+(i mod 60);
      v_product_id:='3';
      else
       dt:=to_date('2018-10-1','yyyy-mm-dd')+(i mod 60);
       v_product_id:='1';
       end if;
    end if;
    V_EMPLOYEE_ID:=CASE I MOD 6 WHEN 0 THEN 11 WHEN 1 THEN 111 WHEN 2 THEN 112
                                WHEN 3 THEN 12 WHEN 4 THEN 121 ELSE 122 END;
    v_order_id:=i;
    v_name := 'xiao' || i;
    v_tel := '18281823545';
    --插入订单
    insert  into ORDERS (ORDER_ID,CUSTOMER_NAME,CUSTOMER_TEL,ORDER_DATE,EMPLOYEE_ID,DISCOUNT)
      values (v_order_id,v_name,v_tel,dt,V_EMPLOYEE_ID,dbms_random.value(100,0));
    --插入描述
      insert  into ORDER_DETAILS (ID,ORDER_ID,PRODUCT_ID,PRODUCT_NUM,PRODUCT_PRICE)
      values (v_order_id,v_order_id,v_product_id,dbms_random.value(1000,0),dbms_random.value(100,0));
  end loop;
end;
```
## 结论分析：
基本掌握了分区表的创建方法和数据的插入，查看等。
