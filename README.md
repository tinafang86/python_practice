# Python_practice
這份專案練習以Kaggle中的[銷售資料](https://www.kaggle.com/datasets/rohitgrewal/restaurant-sales-data)為例作為資料庫操作練習

## 資料清理順序

## 一、匯入資料 

```
import pandas as pd
sales_data = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
print(sales_data)
```

## 二、創建資料

### pd.DataFrame():最基本的創建一個DataFrame物件，可以把它想像為一個Excel表格，有以下幾種常見用法

- 字典 Dicitionary:key為欄位名稱，value為欄位資料
```
import pandas as pd
data = {
    '姓名': ['張三', '李四', '王五'],
    '年齡': [25, 30, 35],
    '城市': ['台北', '高雄', '台中']
}
df = pd.DataFrame(data)
print(df)
```
結果
```
姓名  年齡  城市
0  張三  25  台北
1  李四  30  高雄
2  王五  35  台中
```
***

- 列表List裡的字典
```
import pandas as pd
data = [
    {'姓名': '張三', '年齡': 25, '城市': '台北'},
    {'姓名': '李四', '年齡': 30, '城市': '高雄'},
    {'姓名': '王五', '年齡': 35, '城市': '台中'}
]
df = pd.DataFrame(data)
print(df)
```
結果

```
   姓名  年齡  城市
0  張三  25  台北
1  李四  30  高雄
2  王五  35  台中
```
  
- 使用二維列表 List of List **較難**

```
import pandas as pd
data = [
    ['張三', 25, '台北'],
    ['李四', 30, '高雄'],
    ['王五', 35, '台中']
]
# 使用 columns 參數來指定欄位名稱
columns = ['姓名', '年齡', '城市']
df = pd.DataFrame(data, columns=columns)
print(df)
```
結果

```
   姓名  年齡  城市
0  張三  25  台北
1  李四  30  高雄
2  王五  35  台中
```

## 三、以下爲常用解析資料，初步了解表格資訊

- sales_data.size  # 共有2286個項目，只會考慮列數*欄數，不考慮遺漏值(NaN)
- sales_data.count() #回傳非空值 (non-null)

Order ID          254
Date              254
Product           254
Price             254
Quantity          254
Purchase Type     254
Payment Method    254
Manager           254
City              254
dtype: int64

- sales_data.values  打印出所有的值
- sales_data.index  索引值 # RangeIndex(start=0, stop=254, step=1) 有253列
- sales_data.shape  (行,列) # (254, 9) 25列9欄
- sales_data.columns 回傳每一欄的值
```
Index(['Order ID', 'Date', 'Product', 'Price', 'Quantity', 'Purchase Type',
       'Payment Method', 'Manager', 'City'],
      dtype='object')
sales_data.describe
```

![03](/images/03.png)

- sales_data.dtypes  各欄內值的type
- sales_data.head()  預設前五
- sales_data.tail()  預設後五
```
250 in sales_data.index  # 250為表格中其中一個index
"Product" in sales_data  # True
"Beverages" in sales_data.values
```
- sales_data.info 查看Dtype, memory狀況等等。尤其要注意日期是否為datetime格式

![06](/Users/tinafung8686/Desktop/python_sales-data/image/06)

- sales_date.to_date()
**to_date是一種讀取的格式，不能協助變更type。因此format=xxx必須跟著原始raw data的形式，但若要改變呈現方式可以使用df.strftime()->字串string的格式化時間**

```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
sales_data["Date"] = pd.to_datetime(sales_data["Date"], format="%d-%m-%Y")
sales_data.info()
```
![07](/Users/tinafung8686/Desktop/python_sales-data/image/07)

一氣呵成的變換完格式。parse_date()在找遍哪一欄，date_format()則再決定變更格式

```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", parse_dates=["Date"], date_format = "%d-%m-%Y" )
```

- sales_data.unique() 查看個欄位不重複的值

## 四、專注處理單一或少數欄位

- 我只看Product、Payment Method欄位，透過column name取得

```
import pandas as pd
datas = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=["Product","Payment Method"])
datas
```
![01](/Users/tinafung8686/Desktop/python_sales-data/image/01)

***

- 我只看Product、Payment Method欄位，透過column index取得
```
datas = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=[2,5])
datas
```

## 五、Series被squeeze為純量（無維度）
```datas = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=["Product"]).squeeze("columns")
```
![02](/Users/tinafung8686/Desktop/python_sales-data/image/02)

## 六、調整欄位順序

### 牽涉copy（複製） and view（視圖）概念
### copy

```datas = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv",
                    usecols=["Product", "Payment Method"])
```
#### 方法一：透過新變數定義新資料表。不會操作到原始資料，而是複製到 data_restored 這個新的變數 （更常用）
```
data_restored = datas[["Product", "Payment Method"]]
data_restored = datas[["Payment Method", "Product"]]
```

#### 方法二：使用使用 pd.DataFrame() 建立新物件
```
pd.DataFrame(datas, columns=["Payment Method", "Product"])
```

***

### view
#### 方法一：直接針對原始資料新增一個新欄位
```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
sales_data
sales_data["grade"] = [100,100,100,100] ＃ 新增一個grade欄位
```

#### 方法二：使用.loc或.iloc

- .loc:基於標籤 (label) 進行索引和修改。

df.loc[列的值, 欄的值]

```
import pandas as pd
df = pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]}, index=['x', 'y', 'z'])

# 將索引 'y' 的 'B' 欄位值修改為 99
df.loc['y', 'B'] = 99
print(df)

  A   B
x  1   4
y  2  99
z  3   6
```

```
import pandas as pd

# 創建一個 DataFrame 來記錄學生成績
grades_data = {
    '國文': [85, 92, 78],
    '數學': [90, 88, 95],
    '英文': [75, 80, 82]
}
students = ['小明', '小華', '小強']
grades_df = pd.DataFrame(grades_data, index=students)
print(grades_df)

   國文  數學  英文
小明  85  90  75
小華  92  88  80
小強  78  95  82

```

### 使用 .loc 選取 '小華' 到 '小強' (包含) 的列，和 '國文' 到 '英文' (包含) 的欄

```
subset_loc = grades_df.loc['小華':'小強', '國文':'英文']
print(subset_loc)

```

    國文  英文
小華  92  80
小強  78  82


- .iloc:基於位置 (position) 進行索引和修改。

```
grades_data = {
    '國文': [85, 92, 78],
    '數學': [90, 88, 95],
    '英文': [75, 80, 82]
}
students = ['小明', '小華', '小強']
grades_df = pd.DataFrame(grades_data, index=students)
print(grades_df)
```

   國文  數學  英文
小明  85  90  75
小華  92  88  80
小強  78  95  82

```
score_iloc = grades_df.iloc[1, 1]
print(f"\n小華的數學成績 (iloc)：{score_iloc}")
```
小華的數學成績 (iloc)：88

## 七、整理資料

### 處理文字數據

```
sales_data["Product"].str.lower() #全小寫
sales_data["Product"].str.upper() #全大寫
sales_data["Product"].str.title() #首字大寫，其徐小寫
sales_data["Product"].str.len()
sales_data["Product"].str.strip() # 移除空白
sales_data["Product"].str.lstrip() #移除開頭空白
sales_data["Product"].str.rstrip() #移除結尾空白
sales_data["Product"].str.replace("Beverages", "drinks") #將Beverages全部改為drinks
```

```
sales_data[sales_data["Payment Method"].str.contains("Gift")].head()
```
![08](/Users/tinafung8686/Desktop/python_sales-data/image/08)

```
sales_data[sales_data["Manager"].str.lower().str.startswith("tom")].head()
```

```
import datetime as dt
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", index_col=["Order ID"])
sales_data.columns = sales_data.columns.str.lower()
print(sales_data)
```

### 處理文字數據 - get

```
import datetime as dt
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
sales_data["Manager"].str.split().str.get(0).value_counts() #get=0代表第一個元素，get=1代表第二個元素

Manager
Tom       75
Joao      75
Pablo     46
Walter    30
Remy      28
Name: count, dtype: int64
```

### 處理文字數據 - split(expand = True/False)
```
import datetime as dt
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
sales_data["Manager"].str.split(expand=False)

0        [Tom, Jackson]
1        [Pablo, Perez]
2         [Joao, Silva]
3      [Walter, Muller]
4      [Walter, Muller]
             ...       
249    [Walter, Muller]
250    [Walter, Muller]
251    [Walter, Muller]
252    [Walter, Muller]
253    [Walter, Muller]
Name: Manager, Length: 254, dtype: object

 
sales_data["Manager"].str.split(expand=True, n = 1)
# 根據一個空個分裂

	0	1
0	Tom	Jackson
1	Pablo	Perez
2	Joao	Silva
3	Walter	Muller
4	Walter	Muller
...	...	...
249	Walter	Muller
250	Walter	Muller
251	Walter	Muller
252	Walter	Muller
```


### 處理遺漏值I

#### dropna():刪除遺漏值
- dropna(how = "all")：如果整欄或整列都為遺漏值NaN，就刪去他
- dropna(how = "any")：如果整欄或整列有一個遺漏值NaN，就刪去他

```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv").dropna(how = "all")

# 最後透過size檢查個數是否有增減情況確認遺漏值
sales_data.size
```

#### fillna():填補遺漏值
- fillna(0)：前面常見使用dropna(how = "all")刪除整列0的數據，但那些少數NaN (Not a Number)在表格裡，此時指定他為0
- NaN就算指定為0，在資料型態中仍然算是float，因此建議搭配 astype():()裡面可以放float, int, ...

```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv").dropna(how="all")
sales_data = sales_data.fillna(0).astype(int)
```
***

#### billna():由遺漏值下一位數字填補

- 時間序列資料：例如預測接下來股市資料，可以用後面的數字補值會更準
- 資料延遲：股市交易時因為資料太大來不及運算，會先用NaN補空值，bfill()則可以取一個較能反應最終資料的數值。
- 與ffill為互補關係。例如最開頭的NaN
***

### 處理遺漏值II
- df.isnull():找出空缺值。若值為NaN則為True，否則為False
- df.notnull()：找出非空缺值。若值為非空缺值則為True，否則為False

```
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
missing_value = sales_data["Order ID"].isnull() #找出空缺值
//
0      False
1      False
2      False
3      False
4      False
       ...  
249    False
250    False
251    False
252    False
253    False
Name: Order ID, Length: 254, dtype: bool
//
exist_value = sales_data["Order ID"].notnull() # 找出非空缺值

0      True
1      True
2      True
3      True
4      True
       ... 
249    True
250    True
251    True
252    True
253    True
```
***

### 處理特定資料的遺漏值：subset["",""]子集功能
- 請在 sales_data 中，只檢查 Order ID 這一個欄位。如果某列的 Order ID 欄位是空的（NaN），那麼就將整列資料都移除。
```
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv").dropna(how="all", subset = ["Order ID"])
```
***
### 排序數字
#### sort_values(ascending = True) (由小到大)
- Series的sort_values()
```
import pandas as pd
## 第一種寫法
new_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=["Price"]).sort_values(by = "Price")
## 第二種寫法。squeeze之後表上剩下一欄，所以不用sort by xxx
new_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=["Price"]).squeeze("columns").sort_values()
```
- DataFrame的sort_values()

#### 操作一個欄位
```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
sales_data.sort_values(by = "Price") # 價錢由小到大
sales_data.sort_values(by = "Price", ascending=False) # 價錢由大到小
```
#### 操作多個欄位
```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
sales_data.sort_values(by = ["Price","Quantity"]) # 根據Price由小到大，Quantity也由小到大

sales_data.sort_values(by = ["Price","Quantity"], ascending=[False, True]) # 價錢由大到小，Quantity由小到大

```
#### sort_index(ascending = True) (由小到大)

```
import pandas as pd
df = pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]}, index=['C', 'A', 'B'])
   A  B
C  1  4
A  2  5
B  3  6

df_sorted = df.sort_index(axis = 0) # ascending預設為True，由小到大

        A	B
A	2	5
B	3	6
C	1	4

df_sorted = df.sort_index(axis = 0, ascending = False) # axis = 0針對列索引值排序，axis = 1針對欄索引值排序

        A	B
C	1	4
B	3	6
A	2	5

df_sorted = df.sort_index(axis = 1, ascending = False) # 操作欄，由大到小排序

        B	A
C	4	1
A	5	2
B	6	3
```

#### Series取值
- .get_group()
```
# 將原始資料轉為Series，Product為索引index，Price為欄
data01 = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=["Product", "Price"], index_col=["Product"])
# 發現index重複度很高，用groupby()將一樣的值合併，但需要注意groupby()是分組操作不會直接顯示成果
onlydata = data01.groupby(by = "Product")
# <pandas.core.groupby.generic.DataFrameGroupBy object at 0x1269b6c60>

# 透過mean()先看一下分組狀況，得知欄位包含Beverages、Burgers、Chicken Sandwiches、Fries、Sides & Other 
print(onlydata.mean())

#                         Price
# Product                      
# Beverages            2.950000
# Burgers             12.990000
# Chicken Sandwiches  10.317308
# Fries                3.921569
# Sides & Other        4.990000


# 透過get_group找出burger的價錢
burgerdata = onlydata.get_group("Burgers")
burgerdata

       Price
Product	
Burgers	12.99
Burgers	12.99
Burgers	12.99
Burgers	12.99
Burgers	12.99
Burgers	12.99
Burgers	12.99
Burgers	12.99
Burgers	12.99
Burgers	12.99
Burgers	12.99

```
- .loc()
```
data01 = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=["Product", "Price"], index_col=["Product"])
# 發現index重複度很高，用groupby()將一樣的值合併，但需要注意groupby()是分組操作不會直接顯示成果
onlydata = data01.groupby(by = "Product")

# groupby不可以使用loc
average_data = onlydata.mean()
burgerdata = average_data.loc["Burgers", "Price"] # loc["第一個索引值，取索引標籤"，"第二個索引值，取欄位"]
# np.float64(12.99)

```

#### DataFrame取值
- df[value1]
- df.loc[列的標籤, 欄的標籤] (推薦)

### 計算每一列個數

```
datas = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv",
                    usecols=["Product"])

datas.value_counts()
```

                   Product           
Burgers               52
Chicken Sandwiches    52
Fries                 51
Beverages             50
Sides & Other         49
Name: count, dtype: int64

### 計算每一列個數並換算為比例

```
datas = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv",
                    usecols=["Product"])
datas.value_counts() #計算個數
datas.value_counts(normalize = True) #計算個數並換算為比例 # Return proportions rather than frequencies.

```

### 計算欄位為數字的項目並加總，如Quantity
```
# 計算欄位為數字的項目並加總，如Quantity
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=["Quantity","Price"])
sales_data
```
	Price	Quantity
0	3.49	573.07
1	2.95	745.76
2	4.99	200.40
3	12.99	569.67
4	9.95	201.01
...	...	...

垂直計算，所以會把Price這一列和Quantity這一列的所有值都相加
```
sales_data.sum(axis = 0) 
```
Price         1803.99
Quantity    116995.31
dtype: float64

水平計算，每一列代表Price + Quantity的值
```
sales_data.sum(axis = 1)
```
0      576.56
1      748.71
2      205.39
3      582.66
4      210.96
        ...  
249    205.39
250    767.42
251    291.36
252    633.86
253    680.92
Length: 254, dtype: float64

#### 處理時間

- 匯入時間模組

```
import datetime as dt
```

- date():只處理年/月/日
- time():只處理時/分/秒/微秒
- datetime(): date和time的結合，包含時區(tzinfo)
- timedelta():時間差

```
import datetime as dt
current_time = dt.datetime.now()
two_hours_later = current_time + dt.timedelta(hours=2)
print(f"現在加 2 小時: {two_hours_later}")

# 現在加 2 小時: 2025-08-06 16:20:09.147360
```
### groupby():相當於Excel的樞紐

- groupby是一個分組計劃，不是分組結果的展現。例如出現：<pandas.core.groupby.generic.DataFrameGroupBy object at 0x110f735c0>
- 核心三觀念：分(DataFrame拆成獨立的分組)、應(做四則運算)、結(呈現結過，形成一個新的DataFrame)
- 範例一
```
import pandas as pd

sales_data = pd.DataFrame({
    '團隊': ['A隊', 'B隊', 'A隊', 'B隊', 'C隊', 'A隊'],
    '銷售額': [100, 150, 120, 180, 90, 110],
    '年齡': [25, 30, 28, 35, 22, 26]
})

   團隊  銷售額  年齡
0  A隊  100  25
1  B隊  150  30
2  A隊  120  28
3  B隊  180  35
4  C隊   90  22
5  A隊  110  26

print(sales_data)
# 使用 groupby()，將相同團隊合併並算出分組後的年齡平均值
team_avg_sales = sales_data.groupby('團隊').mean()
print(team_avg_sales)

      銷售額   年齡
團隊                  
A隊  110.0  26.333333
B隊  165.0  32.500000
C隊   90.0  22.000000

```
- 範例二

```
import datetime as dt
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
# 先查看壹下不重複值的狀況，方便我決定誰要當作groupby的項
# Product, City都很適合
sales_data.nunique()
//
Order ID          254
Date               53
Product             5
Price               7
Quantity           29
Purchase Type       3
Payment Method      3
Manager            14
City                5
dtype: int64
//

new_data = sales_data.groupby("Purchase Type")
print(new_data) # <pandas.core.groupby.generic.DataFrameGroupBy object at 0x12b0d04a0>

#查詢三種Purchase Type的第一項
new_data.size()
//
Purchase Type
Drive-thru      61
In-store        86
Online         107
dtype: int64
//
new_data.first() #查看每一個groupby的第一個結果, 同理查看最後一個結果.last()

```
![09](/Users/tinafung8686/Desktop/python_sales-data/image/09)

### 關於index

#### multiindex

- parse_dates["Date"]：主要在告訴pandas讀取檔案時將欄位"Date"視為日期資料處理
- date_format：指定"Date"欄位的格式
- index_col = ["col1", "col2"] ：複合index的精髓
- sort_index()：原本會根據index排序，但若是multiindex(index數>1)，則可以指定要排序哪一個index。sort_index(level = "Country")就是以Country排序，也可以寫sort_index(level = 2)

```
import pandas as pd
bigmac = pd.read_csv("bigmac.csv", parse_dates=["Date"], date_format="%Y-%m-%d", index_col=["Date", "Country"]).sort_index()
bigmac.set_index(keys = ["Date", "Country"])
```

#### get_level_values():取出所有index的值

#### set_names():取出所有index的值
```
# 將Date改為Time
bigmac.index.set_names(names="Time",level=0)
# 將Country改為Location
bigmac.index.set_names(names="Location",level=1)
# 透過List直接改寫index name
bigmac.index = bigmac.index.set_names(names=["Time","Location"])
```
#### sort)index():ascending = True/ False
```
import pandas as pd
bigmac = pd.read_csv("bigmac.csv", parse_dates=["Date"], date_format="%Y-%m-%d", index_col=["Date", "Country"]).sort_index()
bigmac.sort_index(ascending=True)
bigmac.sort_index(ascending=False)
bigmac.sort_index(ascending = [True, False]) #指定每個index的排序狀況，使用list並對照index
```


## 八、資料運算、比較

### 1.map()將同樣規則套用在某變數上面。概念為copy而非view

```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")

#有哪些欄位
agg_product_data = sales_data.groupby(by = "Product").nunique()# 查看不重複清單的值
agg_product_data 

# 新增美味分數
flavor_score = {
          "Beverages":10,
           "Burgers":5,
           "Chicken Sandwiches":3,
           "Fries":6,
           "Sides & Other":2}

sales_data["flavor_score"] = sales_data['Product'].map(flavor_score)
print(sales_data) # 只印出前五筆，方便檢查
```

### 2.資料篩選
#### df.isin():檢查DataFrame或Series中每個元素是否包含在一個你指定的集合
```
# 假設創建一個銷售清單
import pandas as pd
data = {
    'Product': ['Beverages', 'Burgers', 'Fries', 'Beverages', 'Sides & Other'],
    'Quantity': [10, 5, 6, 8, 2],
    'Price': [25, 50, 30, 20, 15]
}

         Product  Quantity  Price
0      Beverages        10     25
1        Burgers         5     50
2          Fries         6     30
3      Beverages         8     20
4  Sides & Other         2     15

sales_data = pd.DataFrame(data)
print(sales_data)
```
***

#### 定義一個我感興趣的清單，並檢查這份清單是否在Product裡面可以找到->isin()

```
products_to_filter = ['Beverages', 'Fries']
is_in_list = sales_data['Product'].isin(products_to_filter)
print(is_in_list) #回傳一個boolean

0     True
1    False
2     True
3     True
4    False
Name: Product, dtype: bool

# 使用布林值進行篩選
filtered_sales = sales_data[is_in_list]
print(filtered_sales)

   Product  Quantity  Price
0  Beverages        10     25
2      Fries         6     30
3  Beverages         8     20
```

#### 根據情境篩選

- 找出string

```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", parse_dates=["Date"], date_format = "%d-%m-%Y" )
sales_data

# 篩出商品為Fries的所有資料
sales_data["Product"] == "Fries" # 出現一列boolean
sales_data[sales_data["Product"] == "Fries" ]
```

- 找出datetime，發現datetime可以像是stirng一樣比大小

```
# 找出所有小於2022/11/15的資料，根據日期欄位由大排到小
sales_data[sales_data["Date"] <= "2022-11-15"].sort_values("Date", ascending=False)
```
- 但若是遇到時間格式為小時, 分, 秒，則需另外處理。dt.time()為24小時制
```
sales_data[sales_data["Time"] <= dt.time(12,0,0)].sort_values("Time")
```
#### 根據特定條件篩選

- 條件都要滿足：AND
```
import datetime as dt
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", parse_dates=["Date"], date_format = "%d-%m-%Y" )
product_is_fries = sales_data["Product"] == "Fries"
city_is_london = sales_data["City"] == "London"
sales_data[product_is_fries & city_is_london].head()
```
- 條件滿足一個即可：OR
- 滿足2個或第三個：AND + OR

**篩出產品為薯條且地區為倫敦者，或者日期> 12/25**

```
import datetime as dt
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", parse_dates=["Date"], date_format = "%d-%m-%Y" )
product_is_fries = sales_data["Product"] == "Fries"
city_is_london = sales_data["City"] == "London"
date_after_1225 = sales_data["Date"] > "2022-12-25"
sales_data[(product_is_fries & city_is_london) |date_after_1225 ].tail()
```


### 3.查詢數值區間

- df[”欄位名稱”].between(startpoint, endpoint)

#### 查詢ID 10452 訂單時間是否介於2022/7/1-2022/7/31

```
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
# 查詢ID 10452 訂單時間是否介於2022/11/1-2022/11/31

# 原地覆蓋值。Date更改格式為datetime, 並改變年月日顯示規則
sales_data["Date"] = pd.to_datetime(sales_data["Date"], format=('%d-%m-%Y'))


id = 10452 #int為10452時會是True, string為"10452"時為False
start_time = pd.to_datetime('01-11-2022')
end_time = pd.to_datetime('30-11-2022')

# 用來篩選布林值
is_match = (sales_data['Order ID'] == id) & (sales_data['Date'].between(start_time, end_time))
print(is_match)

0       True
1      False
2      False
3      False
4      False
       ...  
249    False
250    False
251    False
252    False
253    False

# 根據布林值 Series 篩選 DataFrame中實際的欄位
query_result = sales_data[is_match]
print(query_result)

Order ID	Date	Product	Price	Quantity	Purchase Type	Payment Method	Manager	City
0	10452	2022-11-07	Fries	3.49	573.07	Online	Gift Card	Tom Jackson	London

```

### 4.查詢重複值 df.duplicated()

- df.duplicated()：所有重複值都會被記錄為"True"，不重複值為"False"

```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
sales_data

sales_data["Product"].duplicated()

```

0      False
1      False
2      False
3      False
4      False
       ...  
249     True
250     True
251     True
252     True
253     True
Name: Product, Length: 254, dtype: bool

```
# 印出所有重複的值，所以可以看到表格從index 5開始
sales_data[sales_data["Product"].duplicated()]
```
![04](/Users/tinafung8686/Desktop/python_sales-data/image/04)

- df.duplicated(keep = "first"):預設。首次出現的值會被記錄為True，重複值為False
- df.duplicated(keep = "last"):預設。尾末出現第一次的值會被記錄為True，重複值為False
- ~反轉布林值：篩出第一次出現的值
sales_data[sales_data["Product"].duplicated()]因為這邊會印出所有重複值，反轉換就會出現第一次的不重複值

```
sales_data[~sales_data["Product"].duplicated(keep="first")]

```
![05](/Users/tinafung8686/Desktop/python_sales-data/image/05)

### 5.去除重複值 df.drop_duplicated()

- drop_duplicated()是用來刪除整列一樣的重複列
- drop_duplicated(keep = "first")
- drop_duplicated(keep = "last")

```
import pandas as pd
df = pd.DataFrame({
    'ID': [1, 2, 2, 3, 3],
    'Product': ['A', 'B', 'B', 'C', 'C'],
    'Price': [100, 200, 200, 300, 350]
})
print(df)
unique_df = df.drop_duplicates()
print(unique_df)
```

```
原始 DataFrame：
   ID Product  Price
0   1       A    100
1   2       B    200
2   2       B    200  # 這筆資料與索引 1 完全相同
3   3       C    300
4   3       C    350
------------------------------
預設移除重複後：
   ID Product  Price
0   1       A    100
1   2       B    200
3   3       C    300
4   3       C    350
```

- 同時篩選多個條件

```
import pandas as pd
employees = pd.DataFrame({
    '姓名': ['張三', '李四', '王五', '趙六', '陳七'],
    'Senior Management': [True, False, False, True, False],
    'Team': ['工程', '行銷', '行銷', '工程', '行銷']
})
print(employees)

原始員工資料：
    姓名  Senior Management  Team
0   張三               True  工程
1   李四              False  行銷
2   王五              False  行銷
3   趙六               True  工程
4   陳七              False  行銷

```
情況一：只根據 'Senior Management' 篩選不重複
```
df1 = employees.drop_duplicates(subset=["Senior Management"])
print(df1)

    姓名  Senior Management  Team
0   張三               True  工程
1   李四              False  行銷
```
***

情況二：根據 'Senior Management' 和 'Team' 篩選不重複 (多考慮了"Team")

```
df2 = employees.drop_duplicates(subset=["Senior Management", "Team"])
print(df2)

    姓名  Senior Management  Team
0   張三               True  工程
1   李四              False  行銷

```



























