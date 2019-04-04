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
--------------------------------------------------------------------------------------------------
#### Question 1
Have total emissions from PM2.5 decreased in the United States from 1999 to 2008? Using the <strong>base</strong> plotting system, make a plot showing the total PM2.5 emission from all sources for each of the years 1999, 2002, 2005, and 2008.
```R
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
--------------------------------------------------------------------------------------------------
#### Question 2
Have total emissions from PM2.5 decreased in the <strong>Baltimore City</strong>, Maryland (<font color="red"><strong>fips=="24510"</strong></font>) from 1999 to 2008? Use the <strong>base</strong> plotting system to make a plot answering this question.
```R
# PLOT
par(bg="#FFFFFF")
baltimore <- NEI[NEI$fips=="24510",]
total_emissions <- dplyr::summarise(  dplyr::group_by(baltimore, year), Emissions=sum(Emissions)  )
plot <- barplot(height = total_emissions$Emissions,
                xlab="Time in years", ylab=expression('Total PM'[2.5]*' emissions of Baltimore City in tons'),
                ylim=c(0,3800),
                names.arg = total_emissions$year,
                main=expression('Total PM'[2.5]*' emissions of Baltimore City in tons'),col=c("#C22326","#FDB632","#F37338","#801638"))

## Adding values above the bars
text(x = plot,
     y = round(total_emissions$Emissions,6),
     label = round(total_emissions$Emissions,3),
     pos = 3, cex = 1, col = "black", font=2)
```
--------------------------------------------------------------------------------------------------
#### Question 3
Of the four types of sources indicated by the <font color="red"><strong>type</strong></font> (point, nonpoint, onroad, nonroad) variable, which of these four sources have seen decreases in emissions from 1999–2008 for <strong>Baltimore City</strong>? Which have seen increases in emissions from 1999–2008? Use the <strong>ggplot2</strong> plotting system to make a plot answer this question.
```R
# PLOT
library(ggplot2)
baltimore <- NEI[NEI$fips=="24510",]
total_emissions <- dplyr::summarise(  dplyr::group_by(baltimore, type, year), Emissions=sum(Emissions)  )
ggplot(data=total_emissions, mapping = aes(x=factor(year),y=Emissions,fill=type)) +
    geom_bar(stat="identity") + 
    geom_label(aes(label=round(Emissions,2)), colour = "black", fontface = "bold", fill="white")+
    facet_grid(.~type)+
    xlab("Years")+
    ylab(expression('Baltimore City PM'[2.5]*' emissions in tons'))+
    ggtitle(label=expression('Baltimore City PM'[2.5]*' emissions, faceted by emission type'))+
    theme(plot.title = element_text(hjust = 0.5),legend.position="none")
```
--------------------------------------------------------------------------------------------------
#### Question 4
Across the United States, how have emissions from coal combustion-related sources changed from 1999–2008?
```R
# SCC$EI.Sector lists the sources of combustion
unique(SCC$EI.Sector)

# From the list of unique, the 3 sources given below are the coal combustion sources
# "Fuel Comb - Electric Generation - Coal"
# "Fuel Comb - Comm/Institutional - Coal"
# "Fuel Comb - Industrial Boilers, ICEs - Coal"

sources <- SCC$EI.Sector %in% c(
        "Fuel Comb - Electric Generation - Coal",
        "Fuel Comb - Comm/Institutional - Coal",
        "Fuel Comb - Industrial Boilers, ICEs - Coal")

coal <- NEI$SCC %in% SCC[sources,]$SCC
nei_coal <- NEI[coal,]

total_emissions <- dplyr::summarise(  dplyr::group_by(nei_coal, year), Emissions=sum(Emissions)/1000  )

ggplot(data=total_emissions, mapping = aes(x=factor(year),y=Emissions,fill=year))+
    geom_bar(stat="identity")+
    geom_label(aes(label=round(Emissions,2)), colour = "black", fontface = "bold", fill="white")+
    xlab("Time in years")+
    ylab(expression('Total PM'[2.5]*' emissions in kilo-tons'))+
    ggtitle(label=expression('Emissions of PM'[2.5]*' across United States from coal combustion-related sources'))+
    theme(plot.title = element_text(hjust = 0.5),legend.position="none")
```
--------------------------------------------------------------------------------------------------
#### Question 5
How have emissions from motor vehicle sources changed from 1999–2008 in <strong>Baltimore City</strong>?
```R
# SCC$EI.Sector lists the sources of combustion
unique(SCC$EI.Sector)

# From the list of unique, these 4 sources given below are sources of emissions from motor vehicles
sources <- c(
        "Mobile - On-Road Gasoline Light Duty Vehicles",
        "Mobile - On-Road Gasoline Heavy Duty Vehicles",
        "Mobile - On-Road Diesel Light Duty Vehicles",
        "Mobile - On-Road Diesel Heavy Duty Vehicles")

scc2 <- SCC[ SCC$EI.Sector %in% sources, ]
temp <- NEI[ NEI$SCC %in% scc2$SCC, ]
baltimore <- temp[ temp$fips %in% "24510", ]

total_emissions <- dplyr::summarise( dplyr::group_by(baltimore, year), Emissions=sum(Emissions))

ggplot(data=total_emissions, mapping = aes(x=factor(year), y=Emissions))+
        geom_bar(stat="identity",fill=c("#F9766E","#00BEC6","#00BEC6","#F9766E"))+
        geom_label(mapping = aes(x=factor(year),y=Emissions,label=round(Emissions,2)))+
        xlab("Years")+
        ylab(expression('Baltimore City, Maryland emissions in tons'))+
        ggtitle(label=expression('PM'[2.5]*' emissions from motor vehicle sources in Baltimore City, Maryland'))+
        theme(plot.title = element_text(hjust = 0.5))
```
--------------------------------------------------------------------------------------------------
#### Question 6
Compare emissions from motor vehicle sources in Baltimore City with emissions from motor vehicle sources in <strong>Los Angeles County</strong>, California (<font color="red"><strong>fips=="06037"</strong></font>). Which city has seen greater changes over time in motor vehicle emissions?
```R
scc2 <- SCC[ SCC$EI.Sector %in% sources, ]
temp <- NEI[ NEI$SCC %in% scc2$SCC, ]
bal_los <- temp[ temp$fips %in% c("24510","06037"), ]
bal_los$fips <- as.factor(bal_los$fips)
levels(bal_los$fips) <- c("Los Angeles County, California","Baltimore City, Maryland")

# PLOT
total_emissions <- dplyr::summarise( dplyr::group_by(bal_los, fips, year), Emissions=sum(Emissions) )
ggplot(data=total_emissions, mapping = aes(x=factor(year),y=Emissions,fill=fips))+
        geom_bar(stat="identity")+
        geom_label(aes(label=round(Emissions,2)), colour = "black", fontface = "bold", fill="white")+
        facet_grid(.~fips)+
        xlab("Years")+
        ylab(expression('PM'[2.5]*' emissions in tons'))+
        ggtitle(label=expression('Los Angeles & Baltimore City\'s PM'[2.5]*' emissions in tons'))+
        theme(plot.title = element_text(hjust = 0.5),legend.position="none")
```
--------------------------------------------------------------------------------------------------