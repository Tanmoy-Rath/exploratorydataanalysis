## gh-pages Link : https://tanmoy-rath.github.io/exploratorydataanalysis/
-------------------------------------------------------------------------------------------------
###### Reading the data
```R
NEI <- readRDS("summarySCC_PM25.rds")
SCC <- readRDS("Source_Classification_Code.rds")
```
###### The columns needed for the assignment are NEI$year, NEI$Emissions, NEI$fips, SCC$EI.Sector, SCC$SCC.
###### Checking them for NA values...
```R
c(
sum(is.na(  NEI$year  )),
sum(is.na(  NEI$Emissions  )),
sum(is.na(  NEI$fips  )),
      sum(  NEI$fips==" "  ),
      sum(  NEI$fips==""  ),
sum(is.na(  SCC$EI.Sector  )),
      sum(  SCC$EI.Sector==" "  ),
      sum(  SCC$EI.Sector==""  ),
sum(is.na(  SCC$SCC  )),
      sum(  SCC$SCC==" "  ),
      sum(  SCC$SCC==""  )
)
```
###### All the above sums, were found to be zero, hence there aren't any NA values in those columns.
