## gh-pages Link : https://tanmoy-rath.github.io/exploratorydataanalysis/

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

#### Question 1
Have total emissions from PM2.5 decreased in the United States from 1999 to 2008? Using the <strong>base</strong> plotting system, make a plot showing the total PM2.5 emission from all sources for each of the years 1999, 2002, 2005, and 2008.

```{r plot1, echo=TRUE}
# PLOT
par(bg="#FFFFFF")
total_emissions <- dplyr::summarise(  dplyr::group_by(NEI, year), Emissions=sum(Emissions)  )
plot <- barplot(height = total_emissions$Emissions/1000, # dividing by 1000 converts y-axis to kilo-tons
                xlab="Time in years", ylab=expression('Total PM'[2.5]*' emissions in Kilo-tons'),
                ylim=c(0,8100),
                names.arg = total_emissions$year,
                main=expression('Total PM'[2.5]*' emissions across United States in Kilo-tons'),col=c("#800080","#D896FF","#BE29EC","#660066"))

## Adding values above the bars
text(x = plot,
     y = round(total_emissions$Emissions/1000,6),
     label = round(total_emissions$Emissions/1000,3),
     pos = 3, cex = 1, col = "black", font=2)
```
