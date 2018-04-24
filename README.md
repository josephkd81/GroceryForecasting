# GroceryForecasting

## Abstract
- Feature Engineering
- Keras / GRU

## Background
- FastCampus의 HiringDay에서 다수의 회사들이 언급한 NeuralNetwork를 연습해 봄.

## Project Definition
- Kaggle의 competition 중 Corporacion Favorita Grocery Sales Forecasting의 데이터를 사용함.
- 2013년 1월 1일부터 2017년 8월 15일까지 에콰도르에 있는 소매점 체인 52개 매장의 4000여개 물품들의 판매데이터 약 1억건(103,839,389 datasets)
- 위의 데이터를 바탕으로 2017년 8월 16일부터 31일까지의 각 체인점의 물품별 판매량(unit_sales)을 예측하는 문제.
- 본 프로젝트에서는 Computing power의 제약으로 동일기간 1개 매장의 16개 제품 판매데이터 19600건으로 train dataset을 축소함.
- 본 프로젝트의 evaluate function은 MSE를 사용함. 단, kaggle competition에서는 NWRMSLE 임.

## Methods & Process
- EDA / Feature Engineering
- Preprocessing
- Modeling

## EDA / Feature Engineering
### Holiday_events
- 문제: 같은 날짜에 중복되는 휴일들이 있어서 train dataset과의 merge 시에 dataset의 길이가 늘어나는 현상 발생
- 해결: 'locale'를 기준으로 national / regional / local 의 3가지로 구분하여 중복요소들을 제거하고, 유의미한 정보만 남기는 작업.

### Oil
- 문제: train은 1년 365일 전체에 대한 데이터인데, oil은 월요일부터 금요일까지만 구성. 바로 merge 시 NaN value 발생.
- 해결: 전체 날짜를 포괄하는 calendar를 만들어서 oil dataset도 1년 365일에 해당하는 dataset으로 만들고, NaN value는 앞뒤 날짜의 평균값으로 채움.

### Train
- 문제: dataset의 용량이 사용 컴퓨터의 computing power에 비해 너무 커서 연속된 연산시에 퍼지는 현상이 지속됨.
- 해결: feature engineering과 modeling의 연산을 분리하여 수행함. computing power 분산시킴.
- 앞서 조정한 내용들을 반영하여 holiday_events / oil / items / stores 와 train dataset을 merge한 파일을 csv로 생성함

## Preprocessing
- make a new train dataset: store_no.1 / 16개의 item_nbr 선택하여 train dataset 생성
- OneHotEncoding
- Normalize Series data (oil, unit_sales)
- Split to train/validation
- Convert to np.array

## Modeling
- Keras Sequential: Dense(1:10 / 2:10 / 3:1), activation(1:relu / 2:relu), dropout(0.1)
- Keras GRU: Embedding(20000, 124) / GRU(124) / Dense(1), activation(GRU:tanh, Dense_1:relu), dropout(0.2)
- compile: loss(MSE), optimizer(adam)
- callback: earlystopping, modelcheckpoint

## Results & Discussions
- Sequential: train_mse(0.000211204438944) / val_mse(0.000324253456659)
- GRU: train_mse(0.000276144244329) / val_mse(0.000320386495566)
- 시계열 문제여서 GRU의 성능이 더 좋은것으로 보여짐.

## Conclusion
- pca 적용 과정에서 오류발생하여 보류하였음. pca시에 (-)값이 생성되는 것이 이유로 보임. 더 알아보고 공부할 필요.
- 위의 성능차이가 dropout값의 차이 때문에 과최적화가 예방되어서는 아닌지 변수 조정해서 실행 필요.
- AWS 에서 전체 데이터셋으로 돌려보려고 하였으나, AWS측에서 사용내역이 거의 없어서 p3.8large ec2 instance를 열어줄 수 없다고 함. 이 문제를 해결하고 gpu 사용하여 돌려볼 것.
- NWRMSLE 에 대해  custom evaluate function의 구현 및 적용 연습 필요.
- optimizing에 사용되는 함수들의 의미 이해와 최적함수 설정을 위한 학습 필요.
- 자연어처리에 대한 project로 GRU 활용해보기.
