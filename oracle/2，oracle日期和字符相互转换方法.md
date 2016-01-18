> http://blog.csdn.net/sunlovefly2012/article/details/44059525


# mysql日期和字符相互转换方法: #

|date_format(date,'%Y-%m-%d')    |  `oracle`中的to_char();|
| --------   					| -----:  				| 
|**str_to_date(date,'%Y-%m-%d')**    |  **`oracle`中的to_date();**|


- `%Y`：代表`4`位的年份
- `%y`：代表`2`为的年份
  
- `%m`：代表月, 格式为(`01……12`)  
- `%c`：代表月, 格式为(`1……12`)
  
- `%d`：代表月份中的天数,格式为(`00……31`)  
- `%e`：代表月份中的天数, 格式为(`0……31`) 
  
- `%H`：代表小时,格式为(`00……23`)  
- `%k`：代表 小时,格式为(`0……23`)  
- `%h`： 代表小时,格式为(`01……12`)  
- `%I`： 代表小时,格式为(`01……12`)  
- `%l` ：代表小时,格式为(`1……12`)
   
- `%i`： 代表分钟, 格式为(`00……59`) 
 
- `%r`：代表 时间,格式为`12`小时(`hh:mm:ss [AP]M`)  
- `%T`：代表 时间,格式为`24`小时(`hh:mm:ss`) 
 
- `%S`：代表 秒,格式为(`00……59`)  
- `%s`：代表 秒,格式为(`00……59`) 

# oracle日期和字符相互转换方法: #
- `TO_CHAR` 是把日期或数字转换为字符串
- `TO_DATE` 是把字符串转换为数据库中得日期类型转换函数
- `TO_NUMBER` 将字符转化为数字

## TO_CHAR ##
### 使用`TO_CHAR`函数处理数字  ###
	
- TO_CHAR(number, '格式') 
- TO_CHAR(salary,'$99,999.99'); 

### 使用`TO_CHAR`函数处理日期  ###

- TO_CHAR(date,'格式'); 

## TO_NUMBER ##
### 使用`TO_NUMBER`函数将字符转换为数字  ###

- TO_NUMBER(char[, '格式']) 

## TO_DATE ##
### 使用TO_DATE函数将字符转换为日期  ###

- TO_DATE(char[, '格式']) 

## 格式 ##
- `?` 数字格式格式 
- `9` 代表一个数字 
- `0` 强制显示0 
- `$` 放置一个$符 
- `L` 放置一个浮动本地货币符 
- `.` 显示小数点 
- `,` 显示千位指示符 

## 日期格式  ##
|格式控制                |             描述 |
| ------------	| :-------: |
|YYYY、YYY、YY         |        分别代表4位、3位、2位的数字年 |
|YEAR                  |                年的拼写 |
|MM                    |                数字月 |
|MONTH                 |            月的全拼 |
|MON                   |              月的缩写 |
|DD                    |                 数字日 |
|DAY                   |                星期的全拼 |
|DY                    |                 星期的缩写 |
|AM                    |                表示上午或者下午 |
|HH24、HH12            |        12小时制或24小时制 |
|MI                    |                 分钟 |
|SS                    |                 秒钟 |
|SP                    |                 数字的拼写 |
|TH                    |                数字的序数词 |

## 日期例子： ##
```sql
SELECT TO_DATE('2006-05-01 19:25:34', 'YYYY-MM-DD HH24:MI:SS') FROM DUAL
SELECT TO_DATE('2006-05-01 19:25', 'YYYY-MM-DD HH24:MI') FROM DUAL
SELECT TO_DATE('2006-05-01 19', 'YYYY-MM-DD HH24') FROM DUAL
SELECT TO_DATE('2006-05-01', 'YYYY-MM-DD') FROM DUAL
SELECT TO_DATE('2006-05', 'YYYY-MM') FROM DUAL
SELECT TO_DATE('2006', 'YYYY') FROM DUAL
```
## 日期说明： ##
- 当省略`HH`、`MI`和`SS`对应的输入参数时，`Oracle`使用`0`作为`DEFAULT`值。

- 如果输入的日期数据忽略时间部分，`Oracle`会将时、分、秒部分都置为`0`，也就是说会取整到日。

- 同样，忽略了`DD`参数，`Oracle`会采用1作为日的默认值，也就是说会取整到月。

- 但是，不要被这种“惯性”所迷惑，如果忽略MM参数，`Oracle`并不会取整到年，取整到当前月。

## 注意： ##

### 1. yyyy-MM-dd HH:mm:ss 引起错误 ###
在使用`Oracle`的`to_date`函数来做日期转换时，可能会直觉地采用`yyyy-MM-dd HH:mm:ss`的格式作为格式进行转换，但是在`Oracle`中会引起错误：
```
“ORA 01810 格式代码出现两次”
```
如：
```
select to_date('2005-01-01 13:14:20','yyyy-MM-dd HH24:mm:ss') from dual;
```
原因是SQL中不区分大小写，`MM`和`mm`被认为是相同的格式代码，所以`Oracle`的`SQL`采用了`mi`代替分钟。
```sql
select to_date('2005-01-01 13:14:20','yyyy-MM-dd HH24:mi:ss') from dual;
```
### 2. 另要以24小时的形式显示出来要用HH24 ###
```sql
select to_char(sysdate,'yyyy-MM-dd HH24:mi:ss') from dual; #mi是分钟
select to_char(sysdate,'yyyy-MM-dd HH24:mm:ss') from dual; #mm会显示月份
```

`TO_DATE`格式(以时间:`2007-11-02 13:45:25`为例)

|Year:  |   |	|   |
|--- | :---: | :---: | :---: |
|yy |two digits |两位年           |     显示值:07|
|yyy |three digits |三位年            |    显示值:007|
|yyyy |four digits |四位年          |      显示值:2007|
    
|Month:       |   |	|   |
|--- | :---: | :---: | :---: |
|mm    |number   |  两位月         |     显示值:11	|
|mon   | abbreviated |字符集表示     |     显示值:11月,若是英文版,显示nov    | 
|month |spelled out |字符集表示      |    显示值:11月,若是英文版,显示november |
  
|Day:     |   |	|   |
|--- | :---: | :---: | :---: |
|dd    | number         |当月第几天        |显示值:02|
|ddd   | number         |当年第几天        |显示值:02|
|dy    | abbreviated |当周第几天简写    |显示值:星期五,若是英文版,显示fri|
|day   | spelled out  | 当周第几天全写    |显示值:星期五,若是英文版,显示friday  |      
|ddspth | spelled out, ordinal twelfth |日期的全拼|  |
     
|Hour: |   |	|   |
|--- | :---: | :---: | :---: |
|hh    |two digits |12小时进制           | 显示值:01|
|hh24 |two digits |24小时进制            | 显示值:13|

|Minute:|   |	|   |
|--- | :---: | :---: | :---: |
|mi    |two digits |60进制                |显示值:45|

|Second:|   |	|   |
|--- | :---: | :---: | :---: |
|ss    |two digits |60进制               | 显示值:25|

|其它|   |	|   |
|--- | :---: | :---: | :---: |
|Q     |digit         |季度                |  显示值:4|
|WW    |digit         |当年第几周           | 显示值:44|
|W    |digit          |当月第几周           | 显示值:1|
      
    24小时格式下时间范围为： 0:00:00 - 23:59:59....  
    12小时格式下时间范围为： 1:00:00 - 12:59:59 .... 
## 例子 ##
### 1. 日期和字符转换函数用法（to_date,to_char） ###
```sql         
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') as nowTime from dual; #日期转化为字符串   
select to_char(sysdate,'yyyy') as nowYear from dual; #获取时间的年   
select to_char(sysdate,'mm')as nowMonth from dual; #获取时间的月   
select to_char(sysdate,'dd')as nowDayfrom dual; #获取时间的日   
select to_char(sysdate,'hh24') as nowHour from dual; #获取时间的时   
select to_char(sysdate,'mi')as nowMinute from dual; #获取时间的分   
select to_char(sysdate,'ss')as nowSecond from dual; #获取时间的秒 

select to_date('2004-05-07 13:23:44','yyyy-mm-dd hh24:mi:ss') from dual
```
```sql    
select to_char( to_date(222,'J'),'Jsp') from dual      
``` 
结果显示：  

> Two Hundred Twenty-Two     

### 2. 求某天是星期几 ###
```sql
select to_char(to_date('2002-08-26','yyyy-mm-dd'),'day') from dual; #星期一 
```      
```     
select to_char(to_date('2002-08-26','yyyy-mm-dd'),'day','NLS_DATE_LANGUAGE = American') from dual; #monday 
```     
     
### 3. 设置日期语言 ###
```sql
ALTER SESSION SET NLS_DATE_LANGUAGE='AMERICAN'; #会话级别设置
select to_char(to_date('2002-08-26','yyyy-mm-dd'),'day') from dual;
```           
也可以这样      

> TO_DATE ('2002-08-26', 'YYYY-mm-dd', 'NLS_DATE_LANGUAGE = American')     

### 4. 两个日期间的天数 ###
```sql
select floor(sysdate - to_date('20020405','yyyymmdd')) from dual; #sysdate 当前时间
SELECT floor(3.5) from dual; #floor取小于等于数值n的最大整数  
SELECT ceil(3.5) from dual; #ceil取大于等于数值n的最小整数； 
```
### 5. 时间为null的用法 ###
```sql     
select 1, TO_DATE(null) from dual;        
```
### 6. 月份差 ###
```sql
select months_between(to_date('1999.11.29','yyyy.mm.dd'), to_date('1998.11.29','yyyy.mm.dd')) from dual; #12
    
select months_between(to_date('02-01-1999','MM-DD-YYYY'),to_date('12-31-1998','MM-DD-YYYY')) "MONTHS" FROM DUAL;#1.03225806451613 
```     
### 7. 日期格式冲突问题 ###
```sql
#输入的格式要看你安装的ORACLE字符集的类型, 比如: US7ASCII, date格式的类型就是: '01-Jan-01'      
alter system set NLS_DATE_LANGUAGE = American      
alter session set NLS_DATE_LANGUAGE = American      
#或者在to_date中写      
select to_char(to_date('2002-08-26','yyyy-mm-dd'),'day','NLS_DATE_LANGUAGE = American') from dual;      
注意我这只是举了NLS_DATE_LANGUAGE，当然还有很多，      
可查看      
select * from nls_session_parameters      
select * from V$NLS_PARAMETERS     
```

### 8. 查找2002-02-28至2002-02-01间除星期一和七的天数###
```sql
select count(*)      
from ( select rownum-1 rnum      
	from all_objects      
	where rownum <= to_date('2002-02-28','yyyy-mm-dd') - to_date('2002-02-01','yyyy-mm-dd')+1      
)      
where to_char( to_date('2002-02-01','yyyy-mm-dd')+rnum-1, 'D' ) not in ( '1', '7' )      
```   
> COUNT(*)
> 20

### 9. [DBMS_UTILITY.GET_TIME与DBMS_UTILITY.GET_CPU_TIME比较](http://blog.csdn.net/indexman/article/details/43341253)  ###
> 在前后分别调用DBMS_UTILITY.GET_TIME, 让后将结果相减(得到的是1/100秒, 而不是毫秒).     
 
### 10. Next_day的用法 ###
```sql
Next_day(date, day)      

select next_day(to_date('2016-01-13','yyyy-MM-dd'),'星期五') next_day from dual; #   	
# NEXT_DAY
# 2016/1/15
    
select next_day(sysdate,'星期一') from dual; # 2016/1/18 13:20:12 
# NEXT_DAY(SYSDATE,'星期一')
# 2016/1/25 13:17:24  
```
### 11. 暂时不知道有啥用 ###   
```sql   
select to_char(sysdate,'hh:mi:ss') TIME from all_objects      
#注意：第一条记录的TIME 与最后一行是一样的      
#可以建立一个函数来处理这个问题      
create or replace function sys_date return date is      
begin      
return sysdate;      
end;      

select to_char(sys_date,'hh:mi:ss') from all_objects;   
```     
### 12. 获得小时数,extract()找出日期或间隔值的字段值 ###
```sql     
SELECT EXTRACT(HOUR FROM TIMESTAMP '2001-02-16 2:38:40') from dual# 2
      
select sysdate ,to_char(sysdate,'hh') from dual; 
#	SYSDATE 			TO_CHAR(SYSDATE,'HH')      
------------------   -------------------------      
# 2016/1/18 13:40:31 		01     

select sysdate ,to_char(sysdate,'hh24') from dual;      
#	SYSDATE 			TO_CHAR(SYSDATE,'HH24')      
#------------------  --------------------------      
# 2016/1/18 13:40:31 		13     
```

### 13. 处理月份天数不定的办法 ### 
```sql     
   select to_char(add_months(last_day(sysdate) +1, -2), 'yyyymmdd'),last_day(sysdate) from dual
# TO_CHAR(ADD_MONTHS(LAST_DAY(SY	LAST_DAY(SYSDATE)
------------------------- 	----------------------------
# 		20151201					2016/1/31 13:45:07
     
```
### 14. ADD_MONTHS增加或减去月份 ###
```sql
# add_months(date,要增减的月份（正数为加，负数为减），输出的格式)
select to_char(add_months(to_date('200810','yyyymm'),2),'yyyymm') from dual;
#  TO_CHA
  --------
#  200812
```
### 15. LAST_DAY ###
```sql
# 返回日期的最后一天,参数是个date类型数据。
select last_day(sysdate) from dual;
#   LAST_DAY(SYSDATE)
	-------------------
#   2016/1/31 13:49:31   显示类型和环境变量有关系

select last_day(to_date('20080301','yyyy,MM,dd')) from dual;
#   LAST_DAY
   -----------
#   2008/3/31
```
### 16. 找出今年的天数,月份的天数 ###
```sql      
select add_months(trunc(sysdate,'year'), 12) - trunc(sysdate,'year') from dual # 366  
select last_day(to_date('2016-02','yyyy-mm')) - last_day(to_date('2016-01','yyyy-mm'))  from dual # 29
```

### 17. yyyy与rrrr的区别 ###
```sql
select to_date('99-02','rrrr-mm') from dual # 1999/2/1
select to_date('01-02','rrrr-mm') from dual # 2001/2/1
select to_date('99-02','yyyy-mm') from dual # 0099/2/1
select to_date('01-02','yyyy-mm') from dual # 0001/2/1
```  

### 18. 不同时区的处理 ###
```sql      
select to_char(NEW_TIME(sysdate,'GMT','EST'),'dd/mm/yyyy hh:mi:ss'),sysdate from dual; 
# 18/01/2016 09:11:14 || 2016/1/18 14:11:14
```
### 19. 5秒钟一个间隔###
```sql
Select TO_DATE(FLOOR(TO_CHAR(sysdate,'SSSSS')/300) * 300,'SSSSS') ,TO_CHAR(sysdate,'SSSSS') from dual     
# 2016/1/1 14:15:00  ||  51504
# SSSSS表示5位秒数     
```
### 20. 一年的第几天 ###
```sql
select TO_CHAR(SYSDATE,'DDD'),sysdate from dual       
# 018   ||   2016/1/18 14:20:50   
```
### 21. 计算小时,分,秒,毫秒  ###
```sql         
select Days,A,
       TRUNC(A * 24) Hours,
       TRUNC(A * 24 * 60 - 60 * TRUNC(A * 24)) Minutes,
       TRUNC(A * 24 * 60 * 60 - 60 * TRUNC(A * 24 * 60)) Seconds,
       TRUNC(A * 24 * 60 * 60 * 100 - 100 * TRUNC(A * 24 * 60 * 60)) mSeconds
  from (select trunc(sysdate) Days, sysdate - trunc(sysdate) A from dual)
# 2016/1/18	 ||  0.601296296296296  ||  14  ||  25  ||  52  ||  0
```     
### 22, round舍入到最接近的日期 ###
```sql
select sysdate S1,
       round(sysdate) S2,
       round(sysdate, 'year') YEAR,
       round(sysdate, 'month') MONTH,
       round(sysdate, 'day') DAY # day:舍入到最接近的星期日
  from dual 
# 2016/1/18 14:32:30  ||  2016/1/19  ||  2016/1/1  ||  2016/2/1  ||  2016/1/17

```
### 23, trunc截断到最接近的日期,单位为天，返回的是日期类型 ###
```sql
select sysdate S1,
       trunc(sysdate) S2,              #返回当前日期,无时分秒
       trunc(sysdate, 'year') YEAR,    #返回当前年的1月1日,无时分秒
       trunc(sysdate, 'month') MONTH,  #返回当前月的1日,无时分秒
       trunc(sysdate, 'day') DAY       #返回当前星期的星期天,无时分秒
  from dual
# 2016/1/18 14:38:07  ||  2016/1/18  ||  2016/1/1  ||  2016/1/1  ||  2016/1/17
```
### 24. greatest返回日期列表中最晚日期 ###
```sql
select greatest('01-1月-04','04-1月-04','10-2月-04') from dual # 10-2月-04
```
### 25. 计算时间差 ###
```sql
# 注:oracle时间差是以天数为单位,所以换算成年月,日
select floor(to_number(sysdate - 
		to_date('2007-11-02 15:55:03', 'yyyy-mm-dd hh24:mi:ss')) / 365) as spanYears
  from dual; # 1 # 时间差 - 年
select ceil(months_between(sysdate,
        to_date('2007-11-02 15:55:03', 'yyyy-mm-dd hh24:mi:ss'))) as spanMonths
  from dual; # 15 # 时间差 - 月
select floor(to_number(sysdate - 
		to_date('2007-11-02 15:55:03', 'yyyy-mm-dd hh24:mi:ss'))) as spanDays
  from dual ;# 441 # 时间差 - 天
select floor(to_number(sysdate - 
		to_date('2007-11-02 15:55:03', 'yyyy-mm-dd hh24:mi:ss')) * 24) as spanHours
  from dual; # 10606 # 时间差 - 时
select floor(to_number(sysdate - 
		to_date('2007-11-02 15:55:03', 'yyyy-mm-dd hh24:mi:ss')) * 24 * 60) as spanMinutes
  from dual; # 636412 # 时间差 - 分
select floor(to_number(sysdate - 
		to_date('2007-11-02 15:55:03', 'yyyy-mm-dd hh24:mi:ss')) * 24 * 60 * 60) as spanSeconds
  from dual; # 38184759 # 时间差 - 秒
```

### 26.查找月的第一天,最后一天 ###
```sql
SELECT Trunc(Trunc(SYSDATE, 'MONTH') - 1, 'MONTH') First_Day_Last_Month,
       Trunc(SYSDATE, 'MONTH') - 1 / 86400 Last_Day_Last_Month,
       Trunc(SYSDATE, 'MONTH') First_Day_Cur_Month,
       LAST_DAY(Trunc(SYSDATE, 'MONTH')) + 1 - 1 / 86400 Last_Day_Cur_Month
  FROM dual;
# 2015/12/1  ||  2015/12/31 23:59:59  ||  2016/1/1  ||  2016/1/31 23:59:59

```


