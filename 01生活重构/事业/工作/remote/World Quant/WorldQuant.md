# 6 ways to quickly evaluate a new dataset


> Simulate the below expressions in “None” neutralization and decay 0 setting. And obtain insights of specific parameters using `Long Count` and `Short Count`

| No. | Exp                                                    | Insight                                                                                                                                                                                                                                                                                                       |
| --- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | datafield                                              | %coverage = (Long Count + Short Count)/Universe                                                                                                                                                                                                                                                               |
| 2   | datafield != 0 ? 1 : 0                                 | 1. Coverage, Long Count indicates average non-zero values on a daily basis<br>2. Frequency of <u>unique data</u>                                                                                                                                                                                              |
| 3   | ts_std_dev(datafield,N) != 0 ? 1 : 0                   | Datafields with a quarterly unique data frequency would see a Long Count + Short Count value close to its actual coverage when N = 66 (quarter). When N = 22 (month) Long Count + Short Count would be lower (approx. 1/3rd of coverage) and when N = 5 (week), Long Count + Short Count would be even lower. |
| 4   | abs(datafield) > X                                     | **Bounds** of the datafield. Vary the values of X and see the Long Count. For example, X=1 will indicate if the field is normalized to values between -1 and +1?                                                                                                                                              |
| 5   | ts_median(datafield, 1000) > X                         | **Median** of the datafield over 5 years. Vary the values of X and see the Long Count. Similar process can be applied to check the mean of the datafield.                                                                                                                                                     |
| 6   | X < scale_down(datafield) && scale_down(datafield) < Y | **Distribution** of the datafield. scale_down acts as a MinMaxScaler that can preserve the original distribution of the data. X and Y are values that vary between 0 and 1 that allow us to check how the datafield distribute across its range.                                                              |




