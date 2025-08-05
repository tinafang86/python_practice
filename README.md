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
sales_data.size # 共有2286個項目
sales_data.values # 打印出所有的值
sales_data.index # 索引值 # RangeIndex(start=0, stop=254, step=1) 有253列
sales_data.shape # (行,列) # (254, 9)
sales_data.dtypes # 各欄內值的type
sales_data.head() # 預設前五
sales_data.tail() # 預設後五
250 in sales_data.index #250為表格中其中一個index # True
"Product" in sales_data #Product是否為其中一個欄位名稱 #True
"Beverages" in sales_data.values #Beverages是否為其中一個值 #True

```

## 三、專注處理單一或少數欄位

- 我只看Product、Payment Method欄位，透過column name取得

```
import pandas as pd
datas = pd.read_csv("/Users/tinafung8686/Desktop/python_sales-data/Sales-Data-Analysis.csv", usecols=["Product","Payment Method"])
datas
```
![01](/Users/tinafung8686/Desktop/python_sales-data/image/01)

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

- .iloc:基於位置 (position) 進行索引和修改。

```

```

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
- .get()
- .loc()
- my_series[0]

#### DataFrame取值
- df[value1]
- df.loc[列的標籤, 欄的標籤] (推薦)

