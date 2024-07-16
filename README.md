# Introduction

This case study is adapted from one of the template case studies from
Google’s Data Analytics Certificate Program on Coursera[1].

In this case study, I looked into the ride data for a bike-sharing
company to see if there are any insights in the data that would allow
the company to easily increase its ridership. I used the R programming
language[2] in order to clean the data as well as to make
visualizations. I discovered that members use the service very
differently to casual riders.

## Before Obtaining Data

According to the Bluebikes MediaKit, “Bluebikes is Boston’s regional
bike sharing system that gives members access to more than 4,000+ bikes
(including ebikes as of 2023) and over 400+ stations, designed to offer
a public transportation option, similar to a bus or train” [3].

In this project, I will seek to understand the behavior of users of the
Bluebike service. Specifically, I will answer the question:

“How do annual members and casual riders use Bluebikes differently?”

Using this information, I will come up with potential recommendations
for how the company can increase its ridership. I hope that this
information can prove helpful to the Bluebike company and the Metro
Boston area, which it serves.

# The Data

The data I will use are located in the Bluebikes System Data[4], which
is available to the public, and is used in accordance to the Bluebikes
Data License Agreement. I will be using the last 12 months of data that
are available as of writing; specifically, I will use the data for the
months of July 2023 through June 2024.

The data is formatted as .csv files stored in compressed folders (.zip
files). The .csv files are organized into rows, with each row
representing a unique observation. The column labels include information
such as a ride ID, the type of bike, the start and end times,
information about the start and end stations, and the type of member.
According to the system data description, riders labeled as “casual”
purchased a single trip or a day pass, while riders labeled as “member”
are annual or monthly members.

To assess the bias and credibility of the data, I will use the ROCCC
acronym coined in the Google Data Analytics Certificate Program.

ROCCC stands for

-   Reliable
-   Original
-   Comprehensive
-   Current
-   Cited

The data is original, since it comes from a source provided by the
official website. The data appears to be comprehensize, because it
includes all 12 months from the period of interest, and also every day
within those months; there are hundreds of thousands of rides recorded
for each month, for example the .csv file for the month of July 2023 has
411,510 rows. The data is current, as it includes the 12 most recent
complete months. The data has been cited many times, including by the
Boston Transportation Department[5], as well as members of the public
who have made various projects using the data. I consider the data I am
working with to be reliable, since it has been vetted by the public and
has been cited by government agencies.

This data will help answer my question by providing a starting point
from which I can process the data and gain insights about how members
and casual riders use the service differently.

# Pre Processing

For the most part, I will be using R, as it will allow me to easily
handle the large amount of rows in the data set.

To ensure the data’s integrity, I will save all the code I use to
manipulate it so that I can verify the output using the raw data.

I first extracted the .csv files from the .zip files. This is the only
permanent modification I made to the raw data; the other modifications
were temporary and made using R.

Next, I retrieved the data from the 12 separate files and put them into
12 seperate data frames. Then I combined these data frames into a single
data frame; I also made sure to keep only the relevant columns, namely
the “member\_casual” column which describes whether the rider for a
given trip was a member or a casual rider and the “started\_at” and
“ended\_at” columns which describe the times a particular ride started
and ended, respectively.

    #install.packages("ggplot2") # if package is not installed
    #install.packages("tidyverse") # if package is not installed

    library(ggplot2)
    library(tidyverse)

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ lubridate 1.9.3     ✔ tibble    3.2.1
    ## ✔ purrr     1.0.2     ✔ tidyr     1.3.1
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

    subfolder = "data_copy"

    rides_2023_07 <- read_csv(paste(subfolder, "202307-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2023_08 <- read_csv(paste(subfolder, "202308-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2023_09 <- read_csv(paste(subfolder, "202309-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2023_10 <- read_csv(paste(subfolder, "202310-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2023_11 <- read_csv(paste(subfolder, "202311-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2023_12 <- read_csv(paste(subfolder, "202312-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2024_01 <- read_csv(paste(subfolder, "202401-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2024_02 <- read_csv(paste(subfolder, "202402-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2024_03 <- read_csv(paste(subfolder, "202403-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2024_04 <- read_csv(paste(subfolder, "202404-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2024_05 <- read_csv(paste(subfolder, "202405-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)
    rides_2024_06 <- read_csv(paste(subfolder, "202406-bluebikes-tripdata.csv", sep = "/"),
                              show_col_types = FALSE)

    cleaned_rides <-
      rides_2023_07 %>%
      rbind(rides_2023_08) %>%
      rbind(rides_2023_09) %>%
      rbind(rides_2023_10) %>%
      rbind(rides_2023_11) %>%
      rbind(rides_2023_12) %>%
      rbind(rides_2024_01) %>%
      rbind(rides_2024_02) %>%
      rbind(rides_2024_03) %>%
      rbind(rides_2024_04) %>%
      rbind(rides_2024_05) %>%
      rbind(rides_2024_06)

    cleaned_rides <- subset(cleaned_rides,
                            select=-c(ride_id,
                                      rideable_type,
                                      start_station_name,
                                      start_station_id,
                                      end_station_name,
                                      end_station_id,
                                      start_lat,
                                      start_lng,
                                      end_lat,
                                      end_lng))

I created some new columns calculated from the existing data. I created
a column labeled “ride\_length” by subtracting the values in the
“started\_at” column from those in the “ended\_at” column, and a
“day\_of\_week” column using the “weekdays()” function.

    cleaned_rides <-
      cleaned_rides %>%
        mutate(ride_length = .$ended_at - .$started_at)

    cleaned_rides$day_of_week <- weekdays(cleaned_rides$started_at)

# Analysis

In comparing the behavior of members and casual riders, I used R to look
into the data. The ggplot2 library in particular was very helpful in
making visualizations.

    ggplot(
      data = cleaned_rides,
      mapping = aes(x = fct_relevel(day_of_week, "Monday",
                                                 "Tuesday",
                                                 "Wednesday",
                                                 "Thursday",
                                                 "Friday",
                                                 "Saturday",
                                                 "Sunday"),
                    fill = member_casual)
    ) +
    geom_bar(position=position_dodge()) +
    labs(title = "Rides Taken Throughout The Week",
         x = "Day of Week",
         y = "Number of Rides",
         fill = "Type of Member") +
    scale_fill_manual(values = c("#d95f02","#7570b3"),
                      labels = c("Casual", "Member")) +
    scale_y_continuous(breaks = c(0,
                                  50000, 100000,
                                  150000, 200000,
                                  250000, 300000,
                                  350000, 400000,
                                  450000, 500000))

![](report_files/figure-markdown_strict/visualization%20of%20rides%20by%20day%20of%20week-1.png)

(Colors for all charts chosen using ColorBrewer[6].)

From this graph it is clear that there are far fewer rides by casual
riders than by members. It also seems that casual riders prefer riding
on the weekend, while members ride more from Monday to Friday.

    ggplot(
      data = cleaned_rides %>%
               filter(ride_length < (60 * 60) & # filter out rides longer than or equal to 1 hour
                      ride_length > 0), # filter out rides with length less than or equal to 0 seconds
      mapping = aes(x = ride_length,
                    fill=member_casual)
    ) +
    geom_histogram(alpha=.5,
                   position="identity",
                   breaks = c(0, 2.5 * 60, 5 * 60, 7.5 * 60,
                              10 * 60, 12.5 * 60, 15 * 60, 17.5 * 60,
                              20 * 60, 22.5 * 60, 25 * 60, 27.5 * 60,
                              30 * 60, 32.5 * 60, 35 * 60, 37.5 * 60,
                              40 * 60, 42.5 * 60, 45 * 60, 47.5 * 60,
                              50 * 60, 52.5 * 60, 55 * 60, 57.5 * 60,
                              60 * 60)) +
    labs(title = "Distribution of Ride Lengths",
         x = "Length of Ride (seconds)",
         y = "Number of Rides",
         fill = "Type of Rider") +
    scale_fill_manual(values = c("#d95f02","#7570b3"),
                      labels = c("Casual", "Member")) +
    scale_x_continuous(breaks = c(0, 5 * 60,
                                  10 * 60, 15 * 60,
                                  20 * 60, 25 * 60,
                                  30 * 60, 35 * 60,
                                  40 * 60, 45 * 60,
                                  50 * 60, 55 * 60,
                                  60 * 60)) +
    scale_y_continuous(breaks = c(0,
                                  50000, 100000,
                                  150000, 200000,
                                  250000, 300000,
                                  350000, 400000,
                                  450000, 500000))

![](report_files/figure-markdown_strict/visualization%20of%20length%20of%20rides-1.png)

I have decided to exclude certain values from this initial look at the
data for viewing purposes.

It appears that members tend to take shorter rides than casual riders.
Let’s calculate exactly what the average length of a ride is:

    (cleaned_rides %>%
      filter(ride_length < (60 * 60) & # filter out rides longer than or equal to 1 hour
             ride_length > 0 & # filter out rides with length less than or equal to 0 seconds
             member_casual == "casual"))$ride_length %>%
      mean()

    ## Time difference of 1135.678 secs

    (cleaned_rides %>%
      filter(ride_length < (60 * 60) & # filter out rides longer than or equal to 1 hour
             ride_length > 0 & # filter out rides with length less than or equal to 0 seconds
             member_casual == "member"))$ride_length %>%
      mean()

    ## Time difference of 744.1545 secs

Members take rides that are on average about 400 seconds shorter,
compared to casual riders.

Now, I will take a look at the observations that were filtered out of
the initial analysis. This includes rides that took 0 seconds or less,
and rides that took an hour or more.

    (cleaned_rides %>%
      filter(ride_length <= 0) %>%
      count()) /
      (cleaned_rides %>%
         count())

    ##              n
    ## 1 0.0001429548

The rides that took 0 seconds or less made up between 0.01% and 0.02% of
the total rides from the period of time we are looking at. I will assume
that these observations are measurement errors and exclude them from the
analysis.

    (cleaned_rides %>%
       filter(ride_length >= (60 * 60) &
              ride_length < (6 * 60 * 60)) %>%
       count()) /
      (cleaned_rides %>%
         count())

    ##            n
    ## 1 0.02320985

These longer rides (with length of 1 hour or higher but less than 6
hours) are between 2.3% and 2.4% of the total rides observed, so they
may be worth taking a closer look at.

    ggplot(
      data = cleaned_rides %>%
             filter(ride_length >= (1 * 60 * 60) & # rides 1 hour or longer
                    ride_length < (6 * 60 * 60)),  # that are less than 6 hours long
      mapping = aes(x = ride_length,
                    fill=member_casual)
    ) +
    geom_histogram(alpha=.5,
                   position="identity",
                   breaks = c(60 * 60, 1.5 * 60 * 60,
                              2 * 60 * 60, 2.5 * 60 * 60,
                              3 * 60 * 60, 3.5 * 60 * 60,
                              4 * 60 * 60, 4.5 * 60 * 60,
                              5 * 60 * 60, 5.5 * 60 * 60,
                              6 * 60 * 60)) +
    labs(title = "Distribution of Long Ride Lengths",
         x = "Length of Ride (seconds)",
         y = "Number of Rides",
         fill = "Type of Rider") +
    scale_fill_manual(values = c("#d95f02","#7570b3"),
                      labels = c("Casual", "Member")) +
    scale_x_continuous(breaks = c(60 * 60,
                                  2 * 60 * 60,
                                  3 * 60 * 60,
                                  4 * 60 * 60,
                                  5 * 60 * 60,
                                  6 * 60 * 60)) +
    scale_y_continuous(breaks = c(0,
                                  5000, 10000,
                                  15000, 20000,
                                  25000, 30000,
                                  35000, 40000,
                                  45000))

![](report_files/figure-markdown_strict/outliers%20(large)%20visualization-1.png)

As with the shorter rides I looked at, there is a pattern of casual
riders taking longer rides. It is worth noting that there were more
rides by casual riders that were this length, despite there being more
rides by members overall.

    (cleaned_rides %>%
       filter(ride_length >= (6 * 60 * 60)) %>%
       count()) /
      (cleaned_rides %>%
         count())

    ##             n
    ## 1 0.002860566

The rides of length 6 hours or greater make up between 0.2% and 0.3% of
the total rides.

    seconds_to_period(max(cleaned_rides$ride_length))

    ## [1] "68d 7H 41M 48S"

The longest ride is over 68 days. Given the length of the rides involved
as well as the number, it is unclear if there are any meaningful
interpretations that can be made. I will ignore rides that are 6 hours
or longer in this analysis.

# Findings and Future Research

Based on the analysis, my conclusion is that there are more rides by
members than by casual riders. This may imply that there are more
members than casual riders; in any case, one way to increase ridership
would be with marketing targeted toward non-riders to raise the number
of casual riders, since there don’t appear to be as many.

Casual riders also use the service differently than members of monthly
and yearly passes. Casual riders ride more frequently on the weekend as
opposed to members riding more from Monday to Friday. This suggests that
members use the bikes to get to school or work, while casual riders use
them for fun on the weekends. One potential way to introduce more
members is to introduce an intermediate option between casual and
member: a weekend-membership for users that are not going to use the
bike most days but would be willing to try using them on the weekend.

Another finding is that rides by members tend to be shorter than those
by casual riders. This provides further evidence that casual riders use
the service for more leisure activities (such as long rides on the
weekend) as compared to members who may be using the service for their
daily commute.

One area that may be worth exploring is what the amount of riders (as
opposed to rides) is. Such research would have to be especially careful
to respect the data privacy of the users of the service and of the
people living in the area. This is not possible with the publicly
available data, however it is perhaps something for the company to look
into.

[1] <https://www.coursera.org/professional-certificates/google-data-analytics>

[2] <https://www.r-project.org/about.html>

[3] <https://bluebikes.com/about/media-kit>

[4] <https://bluebikes.com/system-data>

[5] <https://storymaps.arcgis.com/stories/0f5fc6ed107d4c0491d24051eed77ff9>

[6] <https://colorbrewer2.org/>
