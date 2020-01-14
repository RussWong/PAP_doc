# 模型训练与预测

## 模型训练

在完成特征工程，异常检测，特征编码，特征选择之后进行模型训练。基于之前的调研，选择了BayesianRidgeRegression，LassoRegression,GradientBoostingRegression,BaggingRegression,XGBoostRegressor五种模型。

关键代码如下：
```
class Model(luigi.Task):

     def requires(self):
         return FeatureSelection(is_need=self.is_need, type_of_dataset=self.type_of_dataset, name_of_dataset=self.name_of_dataset,
         name_of_target=self.name_of_target, type_of_encoding=self.type_of_encoding, cols_num=self.cols_num,num_of_feature=self.num_of_feature)

     def output(self):
         return {'model' : luigi.LocalTarget('Model/{0}/{1}/{1}_{2}.pkl'.format(self.type_of_dataset, self.name_of_dataset, self.name_of_model),
         format=luigi.format.Nop),
         'log' : luigi.LocalTarget('Model/{0}/{1}/{1}_{2}_log.txt'.format(self.type_of_dataset, self.name_of_dataset, self.name_of_model)),
         'train_X': luigi.LocalTarget('Model/{0}/{1}/{1}_{2}_train_X.csv'.format(self.type_of_dataset, self.name_of_dataset, self.name_of_model))}


     def run(self):
         data = pd.read_csv(self.input()['data'].path)
         model_instance = Model_train(data, self.name_of_target, type_of_dataset=self.type_of_dataset,
         name_of_dataset=self.name_of_dataset, name_of_model=self.name_of_model)
         if self.name_of_model == 'bayesianridge':
             train, test_result, train_X = model_instance.BRreg()
         elif self.name_of_model == 'lasso':
             train, test_result, train_X = model_instance.Lreg()
         elif self.name_of_model =='gradientboosting':
             train, test_result, train_X = model_instance.GBreg()
         elif self.name_of_model =='bagging':
             train, test_result, train_X = model_instance.Breg()
         elif self.name_of_model =='xgboost':
             train, test_result, train_X = model_instance.XGBreg()
         else:
             pass
        
         with self.output()['model'].open('wb') as f:
             joblib.dump(train, f)
        
         with self.output()['log'].open('w') as f:
             for key, values in test_result.items():
                f.write(key + '  ' + str(round(values, 3)) + '\n')
         with self.output()['train_X'].open('w') as f:
             train_X.to_csv(f,index=False)

```

运行如下指令，完成模型训练
```
$  python main.py --local-scheduler Model --name-of-model="*" --name-of-dataset="&"
```
其中&处为已经完成编码的数据集名称，*处目前可选择bayesianridge，lasso，gradientboosting，bagging，xgboost。在不指定模型的情况下，默认模型设置为xgboost。

训练好的模型以及训练的日志数据保存在Model目录下。

例如，数据集'used_house_data_test'属于House类，将该数据集载入不同的模型生成的所有文件都保存在Model/House/used_house_data_test目录下。

该目录下，有

* used_house_data_test_bayesianridge.pkl等保存训练好的模型
* used_house_data_test_bayesianridge_log.txt等记录模型指标RMSE,R2,MAE。

## 模型预测

关键代码如下：
```
class Prediction(luigi.Task):

     def requires(self):
         return Model(is_need=self.is_need, type_of_dataset=self.type_of_dataset, name_of_dataset=self.name_of_dataset,
         name_of_target=self.name_of_target, type_of_encoding=self.type_of_encoding, name_of_model = self.name_of_model,
         cols_num = self.cols_num)

     def output(self):
         pass

     def run(self):
         predictor = joblib.load(self.input()['model'].path)
         data = pd.read_csv('Dataset/{0}/{1}/{2}.csv'.format(self.type_of_dataset, self.name_of_dataset, name_of_dataset_prediction))
         result = predictor.predict(data.drop([self.name_of_target],axis=1))
         np.savetxt('./Result/result.csv',result,delimiter=',',fmt='%f')
         pass

```

运行指令
```
python main.py --local-scheduler Prediction --name-of-dataset-prediction="￥" --name-of-target="#"
```
其中，￥处为新生成的csv文件名，#处为预测标签名，例如Final_Price

预测生成的文件result.csv保存在Result目录。
