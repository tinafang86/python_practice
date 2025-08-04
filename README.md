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





