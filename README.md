# Python_practice
## 這份專案練習以Kaggle中的[銷售資料](https://www.kaggle.com/datasets/rohitgrewal/restaurant-sales-data)為例作為資料庫操作練習

## 一、匯入資料 

```
import pandas as pd
sales_data = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
sales_data
```

## 二、以下爲常用解析資料，初步了解表格資訊

```
sales_data.size  # 共有2286個項目，只會考慮列數*欄數，不考慮遺漏值(NaN)
sales_data.count() #回傳非空值 (non-null)

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

sales_data.values  # 打印出所有的值
sales_data.index  # 索引值 # RangeIndex(start=0, stop=254, step=1) 有253列
sales_data.shape  # (行,列) # (254, 9)
sales_data.dtypes  # 各欄內值的type
sales_data.head()  # 預設前五
sales_data.tail()  # 預設後五
250 in sales_data.index  # 250為表格中其中一個index
"Product" in sales_data  # True
"Beverages" in sales_data.values

```

## 三、專注處理單一或少數欄位

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

## 四、Series被squeeze為純量（無維度）
```datas = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=["Product"]).squeeze("columns")
```
![02](/Users/tinafung8686/Desktop/python_sales-data/image/02)

## 五、調整欄位順序

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
```
  A   B
x  1   4
y  2  99
z  3   6

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

```
   國文  數學  英文
小明  85  90  75
小華  92  88  80
小強  78  95  82

# 使用 .loc 選取 '小華' 到 '小強' (包含) 的列，和 '國文' 到 '英文' (包含) 的欄

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

## 六、整理資料

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

##### 操作一個欄位
```
import pandas as pd
sales_data = pd.read_csv(
    "/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv")
sales_data.sort_values(by = "Price") # 價錢由小到大
sales_data.sort_values(by = "Price", ascending=False) # 價錢由大到小
```
##### 操作多個欄位
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
### 取得特定資料的值

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

```

