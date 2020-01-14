# PAP

本项目基于链家二手房数据集，经过数据处理、特征工程、模型训练及预测等环节，对二手房成交价完成预测，并且对预测结果进行解释和分析。



## 项目流程

本项目一共经过了8个流程：

* 人工预处理（黄宗源）
* 数据预处理（王泽华）
* 数据异常检测（黄宗源）
* 数据编码（黄宗源）
* 特征选择（张健全）
* 模型训练（胡博文、李瑞）
* 模型预测（胡博文、李瑞）
* 模型分析（黄志为）

基于Luigi，将所有流程链接起来，建立流程之间的依赖性，形成工作流。

## 项目目录

    Dataset/
        House/  
        	used_house_data_test/			#数据预处理以及特征工程工作流输出的数据
          
    Model/
    	House/
    		used_house_data_test/			#模型评估以及模型分析工作流输出的结果
    			
    Result/
    		result.csv						#模型预测结果
    	
    main.py									#主函数
    handpreprocessing.py
    missing.py
    fucset.py
    train.py
    analysis.py
    README.md



## 运行方法

* `python -m luigi --module main Hand --local-scheduler` - <font color='red'> 人工预处理</font>，输出原始数据人工预处理后的数据集



* `python -m luigi --module main Preprocessing --local-scheduler` - <font color='red'> 数据预处理</font>，输出数据预处理后的数据集



* `python -m luigi --module main Anomaly --local-scheduler` - <font color='red'> 异常检测</font>，输出异常检测后的数据集

  

* `python -m luigi --module main Encoding --local-scheduler` - <font color='red'> 特征编码</font>，输出target encoding后的数据集



* `python -m luigi --module main FeatureSelection --name-of-feature 15 --local-scheduler` - <font color='red'> 特征选择</font>，输出特征选择后还剩15个最重要特征的数据集



* `python -m luigi --module main BRreg/XGBreg/Lreg/Breg/GBreg --name-of-model 模型名称 --local-scheduler`- <font color='red'>训练模型</font>，模型名称可选`bayesianridge`,`xgboost`,`lasso`,`bagging`,`gradientboosting`



* `python -m luigi --module main Analysis --name-of-model 模型名称 --local-scheduler` - <font color='red'>模型分析</font>，输出该模型中特征对预测结果的贡献图，模型名称可选`bayesianridge`,`xgboost`,`lasso`,`bagging`,`gradientboosting`

