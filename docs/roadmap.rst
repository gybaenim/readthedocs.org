# Transformation of character data into dummy variable 
## Pre-process a model that recommends to-be-brought in player according to player competence 	
		
  		
---  		
		
## Background of analysis 		
### Scenario 		
 		
 - Tottenham manager Mauricio Pochettino who has to bring in a new player to the team on a limited budget wants to bring in a good player who is very aggressive and skillful like Neymar. He assumed that if he identifies players who share similar characteristics with Neymar leveraging player competence information, he could find an aggressive and skillful player. He wants to bring in the best performing players among the players identified on a limited budget. 
	
 - Preprocess the data set on FIFA players before implementing a system to find the best performing players on a limited budget among the type of players he wants. 
		
		
### Objectives 		
		
 - Remove the data with the same name using Distinct function. 
 - Join two tables by implementing Inner Join using Join function. 
 -Store the rest of the part except symbol in Value and Wage variable to Double type using String Split function, and separate the first Position and store it as Position_1 in order to set single position by each player. 
 - Coordinate the unit of Value and Wage variable using Change Value of Refine Data, remove NaN in competence variable, and Value and Wage variable using Simple Filter, and select only necessary variables among above-processed variables using Select Column. 
 - Transform preferred_foot into dummy variable using One Hot Encoder function. 
 - Save the preprocessed data using Unload function. 
		
---		
		
## Brightics process 
<p style = "overflow:auto"> <img src="resources/302_1_fifa_classification_preprocessing/0_modelprocess.PNG" > </p>		
		
		
### Data Load </a>		
 - Load data on FIFA players. 
(<a href="resources/input/302_1_fifa_classification.csv" download>302_1_fifa_classification.csv</a>, <a href="resources/input/302_1_fifa_more_classification.csv" download>302_1_fifa_more_classification.csv</a>)		
		
 - Load 302_fifa _classification data to import information on individual players and their competence/position. 
 - Columns(302_fifa _classification)		
	 - number(Double) : player number 
	 - Name(String) : player name 
	 - Age(Double) : player age
	 - Nationality(String) : player nationality 
	 - Overall(Double) : overall competence score (player competence variable) 
	 - Potential(Double) : potential (player competence variable) 
	 - Club(String) : club that a player belongs to
	 - Value(String) : player value 
	 - Wage(String) : player wage 
	 - Special(Double) : overall player score (player competence variable) 
	 - Acceleration ~ Volleys(Double) : individual competence score (player competence variable) 
	 - Preferred_Positions(String) : player’s preferred position (player position variable) 
	 - RAM ~ LWB(Double) : score by each position (player position variable) 
		
		
	| number |        Name       | Age | Nationality | Overall | Potential | Club                | Value  | Wage  | Special | Acceleration | Aggression | Agility | Balance | Ball_control | Composure | Crossing | Curve | Dribbling | Finishing | Free_kick_accuracy | GK_diving | GK_handling | GK_kicking | GK_positioning | GK_reflexes | Heading_accuracy | Interceptions | Jumping | Long_passing | Long_shots | Marking | Penalties | Positioning | Reactions | Short_passing | Shot_power | Sliding_tackle | Sprint_speed | Stamina | Standing_tackle | Strength | Vision | Volleys | CAM | CB  | CDM | CF  | CM  | ID     | LAM | LB  | LCB | LCM | LDM | LF  | LM  | LS  | LW  | LWB | Preferred_Positions | RAM | RB  | RCB | RCM | RDM | RF  | RM  | RS  | RW  | RWB | ST  |	
	|:------:|:-----------------:|:---:|-------------|---------|-----------|---------------------|--------|-------|---------|--------------|------------|---------|---------|--------------|-----------|----------|-------|-----------|-----------|--------------------|-----------|-------------|------------|----------------|-------------|------------------|---------------|---------|--------------|------------|---------|-----------|-------------|-----------|---------------|------------|----------------|--------------|---------|-----------------|----------|--------|---------|-----|-----|-----|-----|-----|--------|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|---------------------|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|	
	|    0   | Cristiano Ronaldo |  32 | Portugal    | 94      | 94        | Real Madrid CF      | €95.5M | €565K | 2228    | 89           | 63         | 89      | 63      | 93           | 95        | 85       | 81    | 91        | 94        | 76                 | 7         | 11          | 15         | 14             | 11          | 88               | 29            | 95      | 77           | 92         | 22      | 85        | 95          | 96        | 83            | 94         | 23             | 91           | 92      | 31              | 80       | 85     | 88      | 89  | 53  | 62  | 91  | 82  | 20801  | 89  | 61  | 53  | 82  | 62  | 91  | 89  | 92  | 91  | 66  | ST LW               | 89  | 61  | 53  | 82  | 62  | 91  | 89  | 92  | 91  | 66  | 92  |	
	|    1   |      L. Messi     |  30 | Argentina   | 93      | 93        | FC Barcelona        | €105M  | €565K | 2154    | 92           | 48         | 90      | 95      | 95           | 96        | 77       | 89    | 97        | 95        | 90                 | 6         | 11          | 15         | 14             | 8           | 71               | 22            | 68      | 87           | 88         | 13      | 74        | 93          | 95        | 88            | 85         | 26             | 87           | 73      | 28              | 59       | 90     | 85      | 92  | 45  | 59  | 92  | 84  | 158023 | 92  | 57  | 45  | 84  | 59  | 92  | 90  | 88  | 91  | 62  | RW                  | 92  | 57  | 45  | 84  | 59  | 92  | 90  | 88  | 91  | 62  | 88  |	
	|    2   |       Neymar      |  25 | Brazil      | 92      | 94        | Paris Saint-Germain | €123M  | €280K | 2100    | 94           | 56         | 96      | 82      | 95           | 92        | 75       | 81    | 96        | 89        | 84                 | 9         | 9           | 15         | 15             | 11          | 62               | 36            | 61      | 75           | 77         | 21      | 81        | 90          | 88        | 81            | 80         | 33             | 90           | 78      | 24              | 53       | 80     | 83      | 88  | 46  | 59  | 88  | 79  | 190871 | 88  | 59  | 46  | 79  | 59  | 88  | 87  | 84  | 89  | 64  | LW                  | 88  | 59  | 46  | 79  | 59  | 88  | 87  | 84  | 89  | 64  | 84  |	
	|    3   |     L. Suarez     |  30 | Uruguay     | 92      | 92        | FC Barcelona        | €97M   | €510K | 2291    | 88           | 78         | 86      | 60      | 91           | 83        | 77       | 86    | 86        | 94        | 84                 | 27        | 25          | 31         | 33             | 37          | 77               | 41            | 69      | 64           | 86         | 30      | 85        | 92          | 93        | 83            | 87         | 38             | 77           | 89      | 45              | 80       | 84     | 88      | 87  | 58  | 65  | 88  | 80  | 176580 | 87  | 64  | 58  | 80  | 65  | 88  | 85  | 88  | 87  | 68  | ST                  | 87  | 64  | 58  | 80  | 65  | 88  | 85  | 88  | 87  | 68  | 88  |	
	|    4   |      M. Neuer     |  31 | Germany     | 92      | 92        | FC Bayern Munich    | €61M   | €230K | 1493    | 58           | 29         | 52      | 35      | 48           | 70        | 15       | 14    | 30        | 13        | 11                 | 91        | 90          | 95         | 91             | 89          | 25               | 30            | 78      | 59           | 16         | 10      | 47        | 12          | 85        | 55            | 25         | 11             | 61           | 44      | 10              | 83       | 70     | 11      | NaN | NaN | NaN | NaN | NaN | 167495 | NaN | NaN | NaN | NaN | NaN | NaN | NaN | NaN | NaN | NaN | GK                  | NaN | NaN | NaN | NaN | NaN | NaN | NaN | NaN | NaN | NaN | NaN |	
		
		
	> <img src="resources/302_1_fifa_classification_preprocessing/1load.png" width="800" height="450">	
		
		
 - Columns(302_ fifa_ more_ classification)		
	 - name(String) : player name 
	 - international_ reputation(Double) : international reputation 
	 - preferred_foot(String)	
		
	|        name       | international_ | preferred_foot |	
	|:-----------------:|:--------------:|:--------------:|	
	| Cristiano Ronaldo |        5       |      Right     |	
	|      L. Messi     |        5       |      Left      |	
	|       Neymar      |        5       |      Right     |	
	|     L. Suarez     |        5       |      Right     |	
	|      M. Neuer     |        5       |      Right     |	
		
	> <img src="resources/302_1_fifa_classification_preprocessing/2fifamoreload.png" width="800" height="450">	
		
		
		
### Pre-processing		
		
#### Distinct <a href="http://docs.brightics.ai/documentation/function-reference?operator=Distinct" target="_blank"><img src="resources/tool-help.png"></a>		
		
 -Remove 302_fifa_more_classification tables that contain the same player name. 
 - parameter		
	 - Columns : Name	
		
> <img src="resources/302_1_fifa_classification_preprocessing/3distinct.png" width="800" height="450">	
		
#### Join <a href="http://docs.brightics.ai/documentation/function-reference?operator=Join" target="_blank"><img src="resources/tool-help.png"></a>		
		
 - Combine international_reputation, preferred_foot variable in existing FIFA data. 
 - parameter		
	 - In Table : Load, Distinct	
	 - Join Type : Inner Join	
	 - Key Condition(a=B) : Name, Name	
	 - Output Columns : a> all columns except Name, B> international_ reputation, preferred_foot
		
	> <img src="resources/302_1_fifa_classification_preprocessing/4join.png" width="800" height="450">	
		
#### String Split<a href="http://docs.brightics.ai/documentation/function-reference?operator=SplitData" target="_blank"><img src="resources/tool-help.png"></a>		
		
- Separate string in order to remove currency sign € and unit symbol M found in Value variable, and store the separated Value to double type. 
- parameter		
	 - Column : Value	
	 - Hold Columns : All columns except Value 
	 - Delimiter : , (If this symbol is included in the target column, it means that string will be separated by Delimiter, and if “,” is not included here, it does not affect the result) 
	 - Output Column Name : NEW_value	
	 - Output Column Count : 1 (# of columns displayed in Output) 
	 - Output Column Type : Double	
	 - Ignore Start Position : 1 (ignore the first one character) 
	 - Ignore End Position : 1 (ignore the last one character) 
		
> <img src="resources/302_1_fifa_classification_preprocessing/5stringsplit-1.png" width="800" height="450">	
		
#### String Split<a href="http://docs.brightics.ai/documentation/function-reference?operator=SplitData" target="_blank"><img src="resources/tool-help.png"></a>		
		
 - Separate string to remove currency sign € and unit symbol K found in Wage variable and store the separated Value to double type. 
 - parameter		
	 - Column : Wage	
	 - Hold Columns : All columns except Wage 
	 - Delimiter : , (If this symbol is included in the target column, it means that string will be separated by Delimiter, and if “,” is not included here, it does not affect the result) 


	 - Output Column Name : NEW_wage	
	 - Output Column Count : 1 (# of columns displayed in Output) 
	 - Output Column Type : Double	
	 - Ignore Start Position : 1 (ignore one character in start position)
	 - Ignore End Position : 1	(ignore one character in end position)
		
	> <img src="resources/302_1_fifa_classification_preprocessing/5stringsplit-2.png" width="800" height="450">	
		
#### String Split <a href="http://docs.brightics.ai/documentation/function-reference?operator=SplitData" target="_blank"><img src="resources/tool-help.png"></a>		
		
 - There are numerous classes of categories as there is a player with more than one preferred positions in preferred_Positions which is a categorical variable. 
 - Separate the first Position and save it as Position_ 1, Preferred_Positions in order to set one position for each player. 
 - parameter		
	 - Column : preferred_ Positions	
	 - Hold Columns : all columns except preferred_Positions 
	 - Delimiter : " "  (expressed as “ “, but in practice, it means empty. Empty will be used as delimiter for separation) 
	 - Output Column Name : Position	
	 - Output Column Count : 1 (the number of columns displayed in Output and a column name “Output Column Name”_1 designated above is newly created) 	
	 - Output Column Type : String	
		
		
> <img src="resources/302_1_fifa_classification_preprocessing/5stringsplit-3.png" width="800" height="450">	

		
#### Refine Data <a href="http://docs.brightics.ai/documentation/function-reference?operator=RefineData" target="_blank"><img src="resources/tool-help.png"></a>  
 - Refine Data is used to adjust the unit of variables, remove missing values and select variables. 
		
	> <img src="resources/302_1_fifa_classification_preprocessing/6refinedata-0.png" width="800" height="450">	
		
 - Change Value		
	- Value and Wage whose symbol has been separated above have been stored as NEW_ value_ 1 and NEW_ wage_ 1, respectively. 
	- Multiply NEW_value_1 by 10000 and New_wage_1 by 1000 to coordinate the unit, and save the multiplied values to Value and Wage variable. 
	- parameter	
		- Value = NEW_ value_ 1*10000
		- Wage = NEW_ wage_ 1*1000
		
	> <img src="resources/302_1_fifa_classification_preprocessing/6refinedata-1(changevalue).png" width="800" height="450">	
		
 - Simple Filter		
	 - Remove the data in which there is NaN in competence, Value and Wage variable. 
	 - If there is NaN in competence variable, it means that the value of all competence variables is NaN. Therefore, filter Acceleration which is competence variable as a representative. 
	 - If there is NaN in Wage variable, it means that the value of Value is NaN. Therefore, filter Wage variable as a representative. 
	 - parameter	
		 - Acceleration <> NaN
		 - And
		 - Wage <> NaN
	> <img src="resources/302_1_fifa_classification_preprocessing/6refinedata-2(simplefilter).png" width="800" height="450">	
		
 - Select Column		
	 - Select only necessary variables among above-processed variables. 	
	 - parameter	
		 - all variables except number, Photo, Flag, Club_ Logo, NEW_ value_ 1, NEW_ wage_ 1, Preferred_ Positions
		
	> <img src="resources/302_1_fifa_classification_preprocessing/6refinedata-3(selectcolumn).png" width="800" height="450">	
		
		
#### One Hot Encoder <a href="http://docs.brightics.ai/documentation/function-reference?operator=OneHotEncoder" target="_blank"><img src="resources/tool-help.png"></a>		
		
 - Transform preferred_foot which is preferred foot direction variable into dummy variable. 	
		
	 - Inpot Columns : preferred_ foot	
	 - Hold Columns : all variables except preferred_foot	
		
		
	> <img src="resources/302_1_fifa_classification_preprocessing/7onehotencoder.png" width="800" height="450">	
		
		
#### Unload <a href="http://docs.brightics.ai/documentation/function-reference?operator=Unload" target="_blank"><img src="resources/tool-help.png"></a>		
		
 - Save the preprocessed data. 	
 - parameter		
	 - Unload To : /brtc/repo/shared/FIFA_NEW	
		
	> <img src="resources/302_1_fifa_classification_preprocessing/8unload.png" width="800" height="450">	
		
---   		
 		
## Comment  		
		
 - Preprocessed data will be used for future analytics. 
		
		
---  		
		
## Download Data & Model 	
		
### Input data 	
 - <a href="resources/input/302_1_fifa_classification.csv" download>302_1_fifa_classification.csv</a>  		
 - <a href="resources/input/302_1_fifa_more_classification.csv" download>302_1_fifa_more_classification.csv</a>  		
		
### Reference model   		
 - <a href="resources/model/302_1_fifa_classification_preproessing.json" download>302_1_fifa_classification_preproessing.json</a>  		
		

  		
---  		
		
**#Etc** **#Pre-Processing**  **#Distinct** **#Join** **Refind Data** **#Split Data** **#One Hot Encoder** **#Unload** **#FIFA** 		
  		
		
  		
---  		
※ The analytics model in this scenario was written using **Brightics v3.0 suite** version. 
		
		

		
		
		
