資料集描述
本次比賽的資料集（包括訓練集和測試集）由基於貸款預測資料集訓練的深度學習模型產生。特徵分佈與原始資料集接近，但不完全相同。歡迎您在本次比賽中使用原始資料集，以便探索差異，並檢驗在訓練中加入原始資料集是否能提升模型效能。

原始資料集:https://www.kaggle.com/datasets/nabihazahid/loan-prediction-dataset-2025/data

表頭:
id,
annual_income,
debt_to_income_ratio,
credit_score,
loan_amount,
interest_rate,
gender,
marital_status,
education_level,
employment_status,
loan_purpose,
grade_subgrade,
loan_paid_back

目標輸出:預測借款人償還貸款的機率。
提交結果將根據預測機率與觀測目標之間的ROC 曲線下面積進行評估。

敘述式計畫:
目前train.csv的資料都是很明確地告訴我說他有還或是沒還，但我們並非絕對要預測它是0%或100%，只需要回傳一個我認為的機率值就好。
初步觀察來看，各個distribution都還算是常態分度，並無太極端的值，有意思的是(或還蠻正常的?)，大部分都是有上班才來貸款，學歷大致有高中以上，debt consolidation的方式是最多的貸款原因，以及貸款評級的分布是呈現從低到高有遞減的現象的，每上升一級(像是C->B)有階梯式的下降，同一級(如C1、C2)是差不多的

對了，我覺得不需要smote，因為有/無償還得比大約4:1，我覺得還行

與目標變數 (loan_paid_back) 的相關性:
================================================================================
credit_score                  :  0.235
annual_income                 :  0.006
loan_amount                   : -0.004
interest_rate                 : -0.131
debt_to_income_ratio          : -0.336

此表可知目前credit_score和debt_to_income_ratio影響力較大，之後會著重在這兩個的分析上。

再者我認為貸款分級理論上也會是一個很重要的特徵，因為評級越低越不容易還款，我認為高負債比&信用分數差&貸款評級低的組合會嚴重影響償還能力，或許會做一個合成特徵來分析

然後object可能要做label encoding或one hot encoding才能跑模型，有大小關係的像是education_level、grade_subgrade我會做label encoding，而像是loan_purpose、employment_status我會做one hot，除此之外，gender、marital_status，等項目我覺得考慮的意義並不大。

而做完特徵工程後，會採用下列三(四)種模型做分析

LightGBM/XGBoost（通常最佳）
Random Forest
Logistic Regression（當 baseline）

第一次測試是lgb最好，ROC 在 0.92241，第一名是0.92849

我覺得還可以再觀察說貸款評級是否要分這麼細，看他是不是有跟還款有著線性關係
另外一個問題是，要不融合模型看看?
新的ROC:0.92213
結論，第一次得比較好。結案