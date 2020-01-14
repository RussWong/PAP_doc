# 人工数据处理

针对数据集的特异性，进行人工的调整。

主要涉及三个方面:

* 特征提取：将特征字段中有意义的部分提取出来
* 特征调整：根据先验知识，将特征进行调整。比如特征组合重构、无意义特征删除等
* 特定处理：根据特定领域进行的处理操作。如房产数据中地理位置转换为经纬度。

## 项目实例

本房产数据的项目实例中，进行了特征调整与特定处理两方面操作。

**特征调整**

- 特征组合：将“区域”、“路段”、“小区名”组合成“地理位置”一个特征。
- 特征删除：将“交易时间”、“ID”等特征删除。

**特定处理**

- 经纬度转换：通过百度地图提供的API接口，将地理位置转换成经纬度数据。核心代码如下：

```python
def Latlng(location):
	url = 'http://api.map.baidu.com/geocoder?address=' + location +'&output=json'
    html = requests.get(url,headers={'Connection':'close'})
    json1 = BeautifulSoup(html.text,'html.parser')
	text = json.loads(json1.text)
    lat = text['result']['location']['lat']
    lng = text['result']['location']['lng']
    return {'lat':lat,'lng':lng}
```



# 数据预处理

对数据进行预处理操作。

主要涉及四个方面:

* 数据概述：
* 数据去重：去除重复数据。
* 数据标准化：将数值型数据标准化处理。
* 缺失处理：

## 项目实例

**数据概述**

- 功能1
- 功能2
- 待泽华编写

**数据去重**

- 去除重复数据。核心代码如下：

```python
data.drop_duplicates(keep='first', inplace=True)
data.reset_index(drop=True,inplace=True)
```

**数据标准化**

- 数值变量标准化。

  RobustScaler是sklearn库中的一个线性变换器，对存在异常的数据具有较好鲁棒性。

  核心代码如下：

```python
from sklearn.preprocessing import RobustScaler
data[cols] = pd.DataFrame(RobustScaler().fit_transform(data[cols]),columns=cols)
```
**缺失处理**

待泽华编写