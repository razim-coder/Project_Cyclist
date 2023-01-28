---
jupyter:
  kernelspec:
    display_name: R
    language: R
    name: ir
  language_info:
    codemirror_mode: r
    file_extension: .r
    mimetype: text/x-r-source
    name: R
    pygments_lexer: r
    version: 4.0.5
  nbformat: 4
  nbformat_minor: 4
---

::: {.cell .markdown _cell_guid="76ce04c4-f2a4-4974-8aa4-c4e2aa7448c8" _kg_hide-input="false" _kg_hide-output="true" _uuid="1e3025c1-136f-4565-98e2-6338e75d615f" trusted="true"}
# *Bike Sharing Usage Analysis*

# Summary

## \> This project aims to understand the usage and behavior of annual members and casual riders of a bike sharing service, and identify ways to convert casual riders into annual members. Data from 12 months of bike trips is analyzed, including ride duration, frequency, and membership type. The analysis finds that annual members have slightly longer ride durations, take more rides overall, and take more rides per week on average than casual riders. Recommendations include offering incentives for casual riders to purchase annual memberships, targeted digital marketing campaigns, and trial membership options. {#-this-project-aims-to-understand-the-usage-and-behavior-of-annual-members-and-casual-riders-of-a-bike-sharing-service-and-identify-ways-to-convert-casual-riders-into-annual-members-data-from-12-months-of-bike-trips-is-analyzed-including-ride-duration-frequency-and-membership-type-the-analysis-finds-that-annual-members-have-slightly-longer-ride-durations-take-more-rides-overall-and-take-more-rides-per-week-on-average-than-casual-riders-recommendations-include-offering-incentives-for-casual-riders-to-purchase-annual-memberships-targeted-digital-marketing-campaigns-and-trial-membership-options}

# Phase 1: Ask

## Define the problem statement:

Understand the usage and behavior of annual members and casual riders
and identify ways to convert casual riders into annual members.

------------------------------------------------------------------------

# Phase 2: Prepare

## Prepare the data for Further Analysis

-   Import Necessary packages and libraries to R.
-   Import the datasets.
-   Bind all the datasets into a single dataset.
:::

::: {.cell .code _cell_guid="6ea71aac-30fa-4b2d-aaad-cd0175ec8f11" _kg_hide-output="true" _uuid="db235f6a-2d06-4225-94ae-849918c146e4" collapsed="false" jupyter="{\"outputs_hidden\":false}" trusted="true"}
``` R
# Import necessary packages and libraries
install.packages(c("dplyr", "readr", "tidyr","tidyverse","lubridate"))
library(dplyr)
library(readr)
library(tidyr)
library(tidyverse)
library(lubridate)
```
:::

::: {.cell .code _cell_guid="93192478-8392-4f6c-8ff9-f75f5c194463" _uuid="c107cb13-5d39-4383-9487-7c3bbbad4fa5" collapsed="false" jupyter="{\"outputs_hidden\":false}" trusted="true"}
``` R
# Import data (could also use the "lapply" function)
Jan <- read_csv("D:/Projects/Capstone/Track 1/Data/202101-divvy-tripdata.csv")
Feb <- read_csv("D:/Projects/Capstone/Track 1/Data/202102-divvy-tripdata.csv")
Mar <- read_csv("D:/Projects/Capstone/Track 1/Data/202103-divvy-tripdata.csv")
Apr <- read_csv("D:/Projects/Capstone/Track 1/Data/202104-divvy-tripdata.csv")
May <- read_csv("D:/Projects/Capstone/Track 1/Data/202105-divvy-tripdata.csv")
Jun <- read_csv("D:/Projects/Capstone/Track 1/Data/202106-divvy-tripdata.csv")
Jul <- read_csv("D:/Projects/Capstone/Track 1/Data/202107-divvy-tripdata.csv")
Aug <- read_csv("D:/Projects/Capstone/Track 1/Data/202108-divvy-tripdata.csv")
Sep <- read_csv("D:/Projects/Capstone/Track 1/Data/202109-divvy-tripdata.csv")
Oct <- read_csv("D:/Projects/Capstone/Track 1/Data/202110-divvy-tripdata.csv")
Nov <- read_csv("D:/Projects/Capstone/Track 1/Data/202111-divvy-tripdata.csv")
Dec <- read_csv("D:/Projects/Capstone/Track 1/Data/202112-divvy-tripdata.csv")
```
:::

::: {.cell .code _cell_guid="156ffe5f-c222-4ef2-9b74-f4426109b9e3" _uuid="1625d2c0-2017-4271-9c26-1b6db48d8cf0" collapsed="false" jupyter="{\"outputs_hidden\":false}" trusted="true"}
``` R
# Make all data that imported before into a single dataframe and binding rows of every data using 'bind_rows()'
data_list <- list(Jan, Feb, Mar, Apr, May, Jun, Jul, Aug, Sep, Oct, Nov, Dec)
cyclist_data <- bind_rows(data_list)
```
:::

::: {.cell .markdown}
![Pre Data
0.png](vertopal_59f5607a4b394f3099aaba20c72e1f66/2e324ecb-5e3b-4f3b-b465-7b8686d53d73.png)
:::

::: {.cell .markdown}

------------------------------------------------------------------------

# Phase 3: Process

## Cleaning Data:

-   Format the date and time columns
-   create a new column for the duration of the ride
-   Create a column to classify the members into annual and casual.
-   Remove Duplicates
-   Treat Outliers
:::

::: {.cell .code}
``` R
# Format the Date and time using 'ymd_hms()' 
cyclist_data$started_at = ymd_hms(cyclist_data$started_at)
cyclist_data$ended_at = ymd_hms(cyclist_data$ended_at)
```
:::

::: {.cell .code}
``` R
# Create a column for duration using 'difftime()' (Used temporary columns because it was crashing)
cyclist_data$duration = as.numeric(difftime(cyclist_data$ended_at, cyclist_data$started_at, units = "mins"))
```
:::

::: {.cell .code}
``` R
# Create a column for member type using 'ifelse()'
cyclist_data <- cyclist_data %>%
  mutate(member_type = ifelse(member_casual == "casual", "casual","annual"))
```
:::

::: {.cell .code}
``` R
# Removing values that is missing the duration metric as it the key metric
cyclist_data <- cyclist_data %>%
  filter(!is.na(duration))
```
:::

::: {.cell .code}
``` R
# Duplicate values are removed using 'distinct()'
cyclist_data <- cyclist_data %>%
  distinct()
```
:::

::: {.cell .code}
``` R
# Check for outliers using a boxplot
boxplot(cyclist_data$duration, main = "Duration of Rides", xlab = "Duration (minutes)")

# Found that are a few data that are outliers, Proceeding to remove them 
# Removing outliers using the 'quantile()''
cyclist_data <- cyclist_data %>%
  filter(cyclist_data$duration > quantile(cyclist_data$duration, 0.95))
```
:::

::: {.cell .markdown}
![Pre
Data.png](vertopal_59f5607a4b394f3099aaba20c72e1f66/091d12ee-4135-40bc-8cb0-55cfe0ad0793.png)
:::

::: {.cell .markdown}

------------------------------------------------------------------------

# Phase 4: Analyze

## Summarize Data

-   Calculate the Average duration of rides
-   Median duration of rides
-   Total number of rides
-   Average number of rides per week for annual members and casual
    riders
:::

::: {.cell .code}
``` R
# Create a summary of the data
summary_data <- cyclist_data %>%
  group_by(member_type) %>%
  summarise(avg_duration = mean(duration),
            median_duration = median(duration),
            total_rides = n(),
            avg_rides_per_week = total_rides/52)
```
:::

::: {.cell .markdown}
![image.png](vertopal_59f5607a4b394f3099aaba20c72e1f66/cf2a7d5f-044d-4c5f-9808-2a124b05c7fc.png)
:::

::: {.cell .markdown}
## Visualize Data

-   Plot the average duration of rides by member type
-   Plot the total number of rides by member type
-   Plot the average number of rides per week by member type
:::

::: {.cell .code}
``` R
# Plot the average duration of rides by member type
ggplot(cyclist_data, aes(x = member_type, y = duration))+
  geom_boxplot(fill = c("#445E93","#49A078"))+
  ggtitle("Average Duration of Rides by Member Types (Minutes)")+
  xlab("Member Type") + ylab("Duration (Minutes)")+
  theme_minimal()+
  theme(plot.title = element_text(hjust = 0.5, size = 14),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 12)) +
  geom_hline(yintercept = median(cyclist_data$duration), color = "#ffc107", linetype = "dashed")+
  geom_vline(xintercept = c("Customer", "Subscriber"), color = "#ffc107", linetype = "dashed")
```
:::

::: {.cell .markdown}
![Average Duration of Rides by Member
Type.png](vertopal_59f5607a4b394f3099aaba20c72e1f66/66a45625-c8eb-429e-ad61-7ccb934082f9.png)
:::

::: {.cell .code}
``` R
# Plot the total number of rides by member type
ggplot(cyclist_data, aes(x = member_type, fill = member_type))+
  geom_bar(stat = "count", color = "black")+
  ggtitle("Total Number of Rides by Member Type")+
  xlab("Member Type")+
  ylab("Number of Rides")+
  theme_minimal()+
  theme(plot.title = element_text(hjust = 0.5, size = 14),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 12),
        legend.title = element_blank(),
        panel.grid.major = element_line(color = "lightgray", size = 0.5))+
  scale_fill_manual(values = c("#445E93","#49A078"))
```
:::

::: {.cell .markdown}
![total number of rides by member
type.png](vertopal_59f5607a4b394f3099aaba20c72e1f66/5e889ee2-7c25-4b96-b878-9bf1d4399175.png)
:::

::: {.cell .code}
``` R
# Plot the average number of rides per week by member type
ggplot(summary_data, aes(x = member_type, y = avg_rides_per_week)) +
  geom_col(aes(fill = member_type), width = 0.8, color = "black") +
  ggtitle("Average Number of Rides per Week by Member Type") +
  xlab("Member Type") + ylab("Average Number of Rides per Week") +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5, size = 14),
        axis.text = element_text(size = 12),
        axis.title = element_text(size = 12),
        legend.title = element_blank(),
        panel.grid.major = element_line(color = "lightgray", size = 0.5))+
  scale_fill_manual(values = c("#445E93","#49A078"))
```
:::

::: {.cell .markdown}
![Average Number of Rides per Week by
Member.png](vertopal_59f5607a4b394f3099aaba20c72e1f66/46315b28-68eb-4c1b-95b0-be6c9608a50c.png)
:::

::: {.cell .markdown}

------------------------------------------------------------------------

# Phase 5: Share

### The analysis found the following:

1.  The average duration of rides for Casual members is slightly higher
    than for Annual riders.
2.  Casual members take more rides overall than Annual riders.
3.  Casual members also take more rides per week on average than Annual
    riders.
:::

::: {.cell .markdown}

------------------------------------------------------------------------

# Phase 6: Act

### Recommendations

1.  Offer incentives for casual riders to purchase annual memberships,
    such as discounts on long rides or a certain number of free rides
    per month.

2.  Use targeted digital marketing campaigns to reach casual riders and
    highlight the benefits of becoming an annual member.

3.  ## Consider offering a trial membership option for casual riders to try out the service before committing to an annual membership. {#consider-offering-a-trial-membership-option-for-casual-riders-to-try-out-the-service-before-committing-to-an-annual-membership}
:::

::: {.cell .markdown}
# Conclusion

> This analysis provided insight into the usage and behavior of annual
> members and casual riders, and identified potential strategies for
> converting casual riders into annual members. Further research could
> be done to determine the effectiveness of these recommendations and to
> explore other potential factors impacting membership conversions.
:::
