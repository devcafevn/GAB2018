# Recommendation using Azure Machine learning - Phase 1
## 1. Get data
- Download file SaleOrdersFreq.csv in this site.  
- Login to studio.azureml.com and go to Datasets.  
- Import the file to Datasets. 

## 2. Create Experiment to build model
- Create Experiment: Content-BaseRecommend  
- Drag and Drop your dataset into Experiment  

##3. Clean data:
- Expand Data Transformation or search Metadata Editor and drag it into your Experiment
- Launch Column Selector and select FreqBuy column
- Change Data Type to Interger and rename to Rating
- Add "Select Column In Dataset" and select column: CustomerKey, Model, Rating

##4. Build and score Content-Base model
- Add Split component from Data Transformation
- Select "Recommendation Split" for "Spliting Mode"
- Add "Train MatchBox Recommender" into Experiment
- Add "Score Matchbox Recommender" and select "Item Recommendation" for "Recommender Prediction Kind"
- Run Experiment

##5. Publish model and deploy Web Service
- Right click on "Train Matchbox Recommender" and select "Save as Trained Model" to "TrainedModel1"
- Click on "Set up Webservice", select "Predictive service".
- Remove "Split" and Trained Matchbox Recommender" component and add "Web Input" and "Web Output" if it did not remove yet.
- Change Web Input to Score Matchbox Recommender.
- Run Experiment.

# Hybrid Recommendation using Azure Machine learning - Phase 2
## 1. Split data into 3 parts: 
- User Profile (CustomerKey, IncomeGroup, Region)
- Item Features (Model, Property (default 1) 
- and Buy Product: CustomerKey, Model, FreqBuy

## 2. Do the same with above steps to build model and deploy web services.
