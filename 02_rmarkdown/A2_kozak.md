---
title: "A2_kozak"
author: "Julia Kozak"
date: "2024-03-07"
output: 
  html_document:
    toc: true
    toc_depth: 4
    toc_float: 
      collapsed: false
      smooth_scroll: false 
    highlight: kate
    number_sections: false
    keep_md: true 
---




## 1. Loading Things: \

#### 1.1: Loading Packages \
Load in any and all packages required to manipulate, transform, and illustrate the data given.

```r
#Data:
library(cancensus)     #Package of Canadian census data to be used for assignment
```

```
## Warning: package 'cancensus' was built under R version 4.3.2
```

```r
#Functional:
library(tidyverse)     #Package to process our data, stylized formation
```

```
## Warning: package 'tidyverse' was built under R version 4.3.2
```

```
## Warning: package 'ggplot2' was built under R version 4.3.3
```

```
## Warning: package 'purrr' was built under R version 4.3.2
```

```
## Warning: package 'forcats' was built under R version 4.3.2
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.3     ✔ readr     2.1.4
## ✔ forcats   1.0.0     ✔ stringr   1.5.0
## ✔ ggplot2   3.5.0     ✔ tibble    3.2.1
## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
## ✔ purrr     1.0.2     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
library(magrittr)      #Package to help coding sequencing 
```

```
## 
## Attaching package: 'magrittr'
## 
## The following object is masked from 'package:purrr':
## 
##     set_names
## 
## The following object is masked from 'package:tidyr':
## 
##     extract
```

```r
library(janitor)
```

```
## Warning: package 'janitor' was built under R version 4.3.2
```

```
## 
## Attaching package: 'janitor'
## 
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

```r
#Aesthetics:
library(ggplot2)       #Package to generate plotted data
library(sf)
```

```
## Warning: package 'sf' was built under R version 4.3.3
```

```
## Linking to GEOS 3.11.2, GDAL 3.8.2, PROJ 9.3.1; sf_use_s2() is TRUE
```

```r
library(geojsonsf)
```

```
## Warning: package 'geojsonsf' was built under R version 4.3.3
```

```r
library(patchwork)     #Package for extensive plotted data configuration 
```

```
## Warning: package 'patchwork' was built under R version 4.3.2
```

```r
library(gganimate)
```

```
## Warning: package 'gganimate' was built under R version 4.3.3
```

```r
library(magick)
```

```
## Warning: package 'magick' was built under R version 4.3.3
```

```
## Linking to ImageMagick 6.9.12.98
## Enabled features: cairo, freetype, fftw, ghostscript, heic, lcms, pango, raw, rsvg, webp
## Disabled features: fontconfig, x11
```

```r
library(animation)
```

```
## Warning: package 'animation' was built under R version 4.3.3
```

```r
library(ggthemes)      #Package for extra themes, scales, and geoms for plotted data
```

```
## Warning: package 'ggthemes' was built under R version 4.3.2
```

```r
library(RColorBrewer)  #Package to colour plots
library(viridis)       #Package to colour plots
```

```
## Loading required package: viridisLite
```

```
## Warning: package 'viridisLite' was built under R version 4.3.2
```

```r
library(here)          #Package to set working directory via `.Rproj`
```

```
## here() starts at C:/Users/jkoza/Documents/GRADUATE SCHOOL/2. COURSEWORK/GG606/GG606_Assignment_2
```

```r
getwd()                #Function to affirm working directory 
```

```
## [1] "C:/Users/jkoza/Documents/GRADUATE SCHOOL/2. COURSEWORK/GG606/GG606_Assignment_2"
```

#### 1.2: Loading Data 
The data being used here is an R package titled `cancensus` but requires use of an API key (since it is a working library package). We have set a local cache path in order to draw this information much faster. \

*Side note: `install=TRUE` was causing issues, so this line of code is being run each time without it... no more 'overwrite' messages* 

```r
set_cancensus_api_key('CensusMapper_bae5c1c5a3d31a162c898f63dd424bb3')
```

```
## API key set for duration of session. To install your API key for use across sessions, run this function with `install = TRUE`.
```

```r
Sys.getenv("CM_API_KEY")        #confirm API is registering
```

```
## [1] "CensusMapper_bae5c1c5a3d31a162c898f63dd424bb3"
```

```r
set_cancensus_cache_path('01_rawdata') 
```

```
## Cache set for duration of session. To permanently add your cache path for use across sessions, run this function with install = TRUE.
```

```
## [1] "01_rawdata"
```

```r
Sys.getenv("CM_CACHE_PATH")     #confirm cache patch saved to local data folder
```

```
## [1] "01_rawdata"
```


________________________________________________________________________________
## 2. Manipulating Data: \

#### 2.1: Reviewing The Dataset(s) \
There are a few conditions which must be met in order to successfully complete this assignment: \
1. has a name that starts with same letter as your first name or last name (JK); \
2. is comprised of at least 30 geographic units; and \
3. is somewhere that you have not personally visited; and \
4. involves at least three census dates. \


Firstly, we need to see what kind of data is accessible to us within the entire `cancensus` data package of each Canadian census year (1996, 2001, 2006, 2011, 2016, and 2021). I have created these into new data frame objects, but this is just a personal preference in order to more easily re-call upon these vectors and the data columns that they contain. It appears that with each year more data variables were collected with each census date (with the exception of 2001). This makes sense as the ease of accessibility and better record collection practices throughout time are implemented. \

*Side note: if we wanted to do a longer time series we would filter and include only columns that are contained within the 1996 census... if we do not care or are only interested in variables that do not appear earlier within the census history obviously we would not.*

```r
vect1996=list_census_vectors("CA1996")   #1,858 rows of data columns
vect01=list_census_vectors("CA01")       #1,725 rows of data columns
vect06=list_census_vectors("CA06")      #2,175 rows of data columns
vect11=list_census_vectors("CA11")      #3,875 rows of data columns
vect16=list_census_vectors("CA16")      #6,623 rows of data columns
vect21=list_census_vectors("CA21")      #7,709 rows of data columns
```


According to condition one, this assignment must be based on geographic locations that start with the first letter of my given or surname. So, next I want to see what locations within Canada either start with a "J" or "K" and if a particular geographic region appears within all census years, or not. \

I first created a new column for each census regions list to denote what year the data came from and omitted columns that I didn't care about (also no `'duplicate.x'` cols etc.). Since each 'newly' created data set columns were organized in the same order, and I had manually created a new `census_year` column which depicted what year the data came from, I was able to use the `bind_rows` function. Had I **not** generated this new column ahead of time, I would have had to have added additional steps that would have automatically done this, but this way was much easier. 

```r
#Year 1996
CA1996_regions=list_census_regions("CA1996") %>%      #create data frame object
  mutate(census_year="CA1996") #%>%                    #create new col w/ census year
```

```
## Querying CensusMapper API for regions data...
```

```r
#  select(region, name, level, pop, census_year)              #keep these cols only 
#Year 2001
CA01_regions=list_census_regions("CA01") %>%
  mutate(census_year="CA01") 
```

```
## Querying CensusMapper API for regions data...
```

```r
#Year 2006
CA06_regions=list_census_regions("CA06") %>%
  mutate(census_year="CA06")
```

```
## Querying CensusMapper API for regions data...
```

```r
#Year 2011   
CA11_regions=list_census_regions("CA11") %>%
  mutate(census_year="CA11") 
```

```
## Querying CensusMapper API for regions data...
```

```r
#Year 2016
CA16_regions=list_census_regions("CA16") %>%
  mutate(census_year="CA16") 
```

```
## Querying CensusMapper API for regions data...
```

```r
#Year 2021
CA21_regions=list_census_regions("CA21") %>%
  mutate(census_year="CA21") 
```

```
## Querying CensusMapper API for regions data...
```

```r
#Create one new data frame with all census data years by plopping one year after the year on top of each other sequentially and assign it to a data frame name generating a callable object.
all_regions <-                        
  bind_rows(CA1996_regions, CA01_regions, CA06_regions, 
            CA11_regions, CA16_regions, CA21_regions)
```

Condition three states that any location visited by the coder (i.e., me) cannot be used for this assessment. Since I live in, and have therefore visited, the Kitchener-Waterloo tri-state area this location alongside others must be removed from our regional data set. 

```r
all_regions_JK=all_regions %>%                            #create new data frame object
  filter(str_starts(name, "J")|str_starts(name, "K")) %>% #only places with "J" or "K"
  arrange(name) %>%                                       #alphabetized by name
  filter(!(name %in% c("Kelowna", "Kenora", "Kenora, Unorganized", "Kitchener", "Kitchener - Cambridge - Waterloo"))) %>%                 #remove these locations
#Then I wanted to see which of these locations had the highest population among them
  arrange(desc(pop)) %>%    #undoes previous `arrange()` bc sorted by highest population
glimpse()
```

```
## Rows: 1,266
## Columns: 9
## $ region           <chr> "35521", "35521", "35521", "35521", "35521", "3510010…
## $ name             <chr> "Kingston", "Kingston", "Kingston", "Kingston", "King…
## $ level            <chr> "CMA", "CMA", "CMA", "CMA", "CMA", "CSD", "CSD", "CSD…
## $ pop              <int> 172546, 161175, 159561, 152358, 146838, 132485, 12379…
## $ municipal_status <chr> "B", "B", "B", "B", "B", "CY", "CY", "CY", "CY", "C",…
## $ CMA_UID          <chr> NA, NA, NA, NA, NA, "35521", "35521", "35521", "35521…
## $ CD_UID           <chr> NA, NA, NA, NA, NA, "3510", "3510", "3510", "3510", "…
## $ PR_UID           <chr> "35", "35", "35", "35", NA, "35", "35", "35", "35", "…
## $ census_year      <chr> "CA21", "CA16", "CA11", "CA06", "CA01", "CA21", "CA16…
```

It appears as though the geographic region of Kingston, ON consistently had the highest population count(s) throughout census years 2001-2021. However, I quickly double checked to see if any population data from the 1996 census year existed but for some reason was just not quite as high. \
This did in fact return a search hit, and since Kingston is a.) a geographic region sampled throughout all available census years b.) is a place that I have *not* visited c.) starts with the first letter of my surname and d.) has (mostly) the highest population counts for a location meeting the above criteria (and over 30 geographic units), I chose to focus my population analysis on this region. 


```r
all_regions_JK %>%
  filter(name=="Kingston", census_year== "CA1996")
```

```
## # A tibble: 3 × 9
##   region  name    level   pop municipal_status CMA_UID CD_UID PR_UID census_year
##   <chr>   <chr>   <chr> <int> <chr>            <chr>   <chr>  <chr>  <chr>      
## 1 3510011 Kingst… CSD   55947 C                35521   3510   35     CA1996     
## 2 3510009 Kingst… CSD   43756 TP               35521   3510   35     CA1996     
## 3 1305014 Kingst… CSD    2873 PAR              13310   1305   13     CA1996
```

```r
#Slim down the data frame even further to only contain region area codes for the city of Kingston, only with census years 2001-2021 (check that they are the same?).. I removed 1996 from the df after the following section did not contain education data
all_regions_Kingston=all_regions_JK %>%
  filter(name=="Kingston") %>%
  filter(census_year!="CA1996") %>%
  arrange(census_year) 
```


#### 2.2: Composing The Main Dataset(s) \

Since I have chosen to look at changes in population demographics related to Kingston, ON and it includes data from the 1996 census, this somewhat limits the type of data that we are able to look at over time. However, based on how overwhelming all of the new data variables that are available to us since 2011 (3,000+) this is now much easier to narrow down on what is accessible to us over time. Here is where one would look at all of the variables collected from the 1996 census and go, "Okay, so what is it that I actually wanna know? What kind of story am I gonna try and tell with this data? Is there one? Where is it and with what?" \

What I knew of Kingston consisted of not much, only that there were a couple of universities located there, based on three other Msc/PhD graduate students I had met in my travels. After a quick google search, it appears that Kingston apparently has more PhD holders per capita than any other major Canadian city [(The Globe and Mail 2022).](https://www.theglobeandmail.com/business/adv/article-kingston-ontario-a-small-place-with-big-city-features/) So assuming that I want to look at stuff related to this, I needed to figure out what information vectors were available to me based on this general topic. \

Looking at the data and variables available to us in 1996 were... not very impressive. I was not very pleased with them, especially after looking at all of the different and expansive data available from the 2001 census. Once I saw all of these vectors detailing post secondary qualifications and degree topics I knew I wanted to cut out 1996 from the analysis. Sorry not sorry. Here, I would start building some data frames that I would want to use to illustrate this data **at the CMA regional level to then be broken down into CSD and DAs.** \


```r
#Find any census topic from 2001 that relates to education for any gender or age.. I later added the sub_vectors for males and females to this dataset afterwards
CA01_ed_vect=find_census_vectors("education", dataset="CA01", type=c("total","male", 
                                  "female"), query_type="keyword", 
                                 interactive=FALSE)              #returns 54 vec
#Search  function did not return all results when filtering 'education' from `vect01`

#Find any census topic from 2006 that relates to education for any gender or age
CA06_ed_vect=find_census_vectors("education", dataset="CA06", type=c("total","male", 
                                  "female"), query_type="keyword", 
                                 interactive=FALSE)              #returns 96 vec
#Find any census topic from 2011 that relates to education for any gender or age
CA11_ed_vect=find_census_vectors("education", dataset="CA11", type=c("total","male", 
                                  "female"), query_type="keyword", 
                                 interactive=FALSE)             #returns 126 vec
#Find any census topic from 2016 that relates to education for any gender or age
CA16_ed_vect=find_census_vectors("education", dataset="CA16", type=c("total","male", 
                                  "female"), query_type="keyword", 
                                 interactive=FALSE)             #returns 549 vec
#Find any census topic from 2021 that relates to education for any gender or age
CA21_ed_vect=find_census_vectors("education", dataset="CA21", type=c("total","male", 
                                  "female"), query_type="keyword", 
                                 interactive=FALSE)             #returns 696 vec
#Vectors related to this search increase with census years...
```

*Side note: The tricky thing to look out for are vectors that are sub-vectors of initial parent ones... Ex: 'Total population 20 years and over by highest level of schooling' (`v_CA01_1384`) is an initial parent vector that gives birth to a series of sub-vectors like 'Grades 9 to 13' (`v_CA01_1386`), that then trickle down to even DEEPER sub-vectors... like 'With/Without high school graduation certificate' (`v_CA01_1388`/`v_CA01_1387`). This is also only a case and point example for 2001.* \

Ugh. I have a feeling that I am going to have to be pulling out specific vectors from each census year because a.) vectors are not consistent throughout years (ex. people who have Masters degrees have different vector codes) and b.) details or descriptions surrounding the same thing are different too.  


```r
#I thought it was important to keep the parent_vector information handy in case I wanted to go back and reference where those numbers came from. Had to first defineand create the object before merging and rearranging though. 

#Add in `parent_vector` col to previous 2001 education vector data set
CA01_ed_vect=CA01_ed_vect %>%                           #overwrite previous object
  mutate(merge(CA01_ed_vect, vect01[, c("vector", "parent_vector")], 
               by="vector")) %>%                  #merge by vector and add new col
  select(vector, type, label, parent_vector, everything())            #reorder cols
#Add in `parent_vector` col to previous 2006 education vector data set
CA06_ed_vect=CA06_ed_vect %>%                     
  mutate(merge(CA06_ed_vect, vect06[, c("vector", "parent_vector")], 
               by="vector")) %>%                  
  select(vector, type, label, parent_vector, everything()) 
#Add in `parent_vector` col to previous 2011 education vector data set
CA11_ed_vect=CA11_ed_vect %>%                    
  mutate(merge(CA11_ed_vect, vect11[, c("vector", "parent_vector")], 
               by="vector")) %>%                  
  select(vector, type, label, parent_vector, everything()) 
#Add in `parent_vector` col to previous 2016 education vector data set
CA16_ed_vect=CA16_ed_vect %>%                    
  mutate(merge(CA16_ed_vect, vect16[, c("vector", "parent_vector")], 
               by="vector")) %>%                  
  select(vector, type, label, parent_vector, everything()) 
#Add in `parent_vector` col to previous 2021 education vector data set
CA21_ed_vect=CA21_ed_vect %>%                    
  mutate(merge(CA21_ed_vect, vect21[, c("vector", "parent_vector")], 
               by="vector")) %>%                  
  select(vector, type, label, parent_vector, everything()) 

#Note: the merge made additional parent vector cols .x and .y but because they are the same and it doesn't really matter + I was too lazy to code them out, I just left them as is. 
```

Now we have to take all of the vectors we identified in the previous section and get the corresponding population data from the desired region (Kingston). I have made data frames for population data broken down by CSD regions (not very detailed or descriptive due to a lack of total regions within the area), and DAs (more detailed, can see more of a neighborhood effect within each CSD).  

*Side note: could not get the `mutate(census_year="CA11")` function to work in conjunction with `get_census()` most likely because it was pulling the data and making a new table... to then add onto though...?*


```r
#Get 2001 population data from selected vectors for the Kingston region at the CSD level
CA01_ed_CSD=get_census(dataset="CA01", regions=list(CMA="35521"), 
            vectors=c(CA01_ed_vect$vector), level="CSD")
```

```
## Reading vectors data from local cache.
```

```r
            #64 cols (10 given + 54 vectors) appears to have synced properly 
#Get 2006 population data from selected vectors for the Kingston region
CA06_ed_CSD=get_census(dataset="CA06", regions=list(CMA="35521"),
            vectors=c(CA06_ed_vect$vector), level="CSD")
```

```
## Reading vectors data from local cache.
```

```r
            #106 cols (10 given + 96 vectors)
#Get 2011 population data from selected vectors for the Kingston region
CA11_ed_CSD=get_census(dataset="CA11", regions=list(CMA="35521"), 
            vectors=c(CA06_ed_vect$vector), level="CSD")
```

```
## Reading vectors data from local cache.
```

```r
#Get 2016 population data from selected vectors for the Kingston region
CA16_ed_CSD=get_census(dataset="CA16", regions=list(CMA="35521"), 
            vectors=c(CA06_ed_vect$vector), level="CSD") 
```

```
## Reading vectors data from local cache.
```

```r
#Get 2021 population data from selected vectors for the Kingston region
CA21_ed_CSD=get_census(dataset="CA21", regions=list(CMA="35521"), 
            vectors=c(CA21_ed_vect$vector), level="CSD") 
```

```
## Reading vectors data from local cache.
```


```r
#I like having the data wide, but I need it to be long in order to more readily see the vectors so that I can pull whichever ones I want out + for plotting(?)

#Edit 2001 census education data into a more malleable form
CA01_ed_CSD_long=CA01_ed_CSD %>%                        #create new object
  clean_names() %>%                                     #clean col names
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, dwellings, 
                         households, cd_uid, pr_uid, cma_uid), #all but NOT these cols
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),#new col w/ code
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) #remove from old col
#Edit 2006 census education data into a more malleable form
CA06_ed_CSD_long=CA06_ed_CSD %>%                       
  clean_names() %>%                                     
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, dwellings, 
                         households, cd_uid, pr_uid, cma_uid), 
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) 
#Edit 2011 census education data into a more malleable form
CA11_ed_CSD_long=CA11_ed_CSD %>%                       
  clean_names() %>%                                     
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, dwellings, 
                         households, cd_uid, pr_uid, cma_uid), 
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) 
#Edit 2016 census education data into a more malleable form
CA16_ed_CSD_long=CA16_ed_CSD %>%                       
  clean_names() %>%                                     
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, dwellings, 
                         households, cd_uid, pr_uid, cma_uid), 
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) 
#Edit 2021 census education data into a more malleable form
CA21_ed_CSD_long=CA21_ed_CSD %>%                       
  clean_names() %>%                                     
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, dwellings, 
                         households, cd_uid, pr_uid, cma_uid), 
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) 
```

*Side note: I was gonna be lazy and not use the `clean_names()` function... but then ran into an issue with 'Area (sq km)' which I would have otherwise have had to enter in as an expression, which would have been an absolute pain that I did not feel like getting into. I also had to somehow extract the vector code better than using the `<=?` method since there were so many underscores, which sucked, but appears to be working properly now.*


```r
#Get 2001 population data from selected vectors for the Kingston region at the DA level
CA01_ed_DA=get_census(dataset="CA01", regions=list(CMA="35521"), 
            vectors=c(CA01_ed_vect$vector), level="DA")
```

```
## Reading vectors data from local cache.
```

```r
#Get 2006 population data from selected vectors for the Kingston region
CA06_ed_DA=get_census(dataset="CA06", regions=list(CMA="35521"),
            vectors=c(CA06_ed_vect$vector), level="DA")
```

```
## Reading vectors data from local cache.
```

```r
#Get 2011 population data from selected vectors for the Kingston region
CA11_ed_DA=get_census(dataset="CA11", regions=list(CMA="35521"), 
            vectors=c(CA06_ed_vect$vector), level="DA")
```

```
## Reading vectors data from local cache.
```

```r
#Get 2016 population data from selected vectors for the Kingston region
CA16_ed_DA=get_census(dataset="CA16", regions=list(CMA="35521"), 
            vectors=c(CA06_ed_vect$vector), level="DA") 
```

```
## Reading vectors data from local cache.
```

```r
#Get 2021 population data from selected vectors for the Kingston region
CA21_ed_DA=get_census(dataset="CA21", regions=list(CMA="35521"), 
            vectors=c(CA21_ed_vect$vector), level="DA") 
```

```
## Reading vectors data from local cache.
```


```r
#Edit 2001 census education data into a more malleable form.. same thing as CSD
CA01_ed_DA_long=CA01_ed_DA %>%
  clean_names() %>%                                     #clean col names
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, 
                       dwellings, households,  csd_uid, cd_uid, ct_uid, cma_uid),
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),#new col w/ code
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) #remove from old col
#Edit 2006 census education data into a more malleable form.. same thing as CSD
CA06_ed_DA_long=CA06_ed_DA %>%
  clean_names() %>%                                     
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, 
                       dwellings, households,  csd_uid, cd_uid, ct_uid, cma_uid),
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) 
#Edit 2011 census education data into a more malleable form.. same thing as CSD
CA11_ed_DA_long=CA11_ed_DA %>%
  clean_names() %>%                                     
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, 
                       dwellings, households,  csd_uid, cd_uid, ct_uid, cma_uid),
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) 
#Edit 2016 census education data into a more malleable form.. same thing as CSD
CA16_ed_DA_long=CA16_ed_DA %>%
  clean_names() %>%                                     
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, 
                       dwellings, households,  csd_uid, cd_uid, ct_uid, cma_uid),
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) 
#Edit 2021 census education data into a more malleable form.. same thing as CSD
CA21_ed_DA_long=CA21_ed_DA %>%
  clean_names() %>%                                     
  pivot_longer(cols=!c(geo_uid, type, region_name, area_sq_km, population, 
                       dwellings, households,  csd_uid, cd_uid, ct_uid, cma_uid),
               names_to= "vector_des",
               values_to= "count") %>%
   mutate(vector_code=str_extract(vector_des, "^v_[^_]*_[0-9]+_"),
          vector_des=str_remove(vector_des, "^v_[^_]*_[0-9]+_")) 
```


________________________________________________________________________________
## 3. Plotting Data: \

#### 3.1: Reviewing The Dataset(s)... Again \
Since we have successfully pulled and saved the data to our local cache (I hope), we can now actually look at the data across these census years and try to plot some stuff out. As I mentioned earlier, there is an issue with similar census data vectors that are essentially the same thing, but they go by different vectors or have been slightly modified in spirit (ex. number of people who have a Msc degree). So here is where our plotting story begins. \

1. After looking at all of this data, my intuition would be to first look at general changes in education throughout the region over the years. Do we see more and more people throughout time getting higher and higher education levels? Levels to be broken down based on whether they were **granted not just attended:** i.) no high school ii.) high school iii.) trades school iv.) bachelor's v.) Master's vi.) PhD
2. Do we see changes in the types or popularity of post-secondary degrees over time? Throughout the region?
3. Focusing in on one type (xx) who is getting more of these? Males, females? Older? Younger? 


When this change in PhD holders occur? has there been an increasing amount of people with bachelors degrees being granted, then into phds?



##### 3.1.1: Are There Changes in Post-Secondary Degree levels? \
As was mentioned, the main CSD and DA data sets need to be adjusted to meet each need... I am not sure how one would automate this process, since Statistics Canada themselves seem to choose whatever the hell it is that they want to call each census vector, and that they are not consistent throughout time (partially to denote census year).  Nonetheless, I sifted through each education census vector list and tried to find data that best corresponded to those who were awarded and are bachelor degree holders throughout each census year. However, the conditions change ever so slightly throughout each year (denoted below).    

```r
#We can use the long education vector lists to find vectors that are best suited to our conditions. However, we still need to grab the affiliated shape files, which I did not do when pulling the CSD or DA data... whomp whomp. 

#2001 bachelor degree or higher for 20 yrs old+
CA01_bachelor=get_census(dataset='CA01', regions=list(CMA="35521"),
                         vectors=c("bachelor_degree_higher"="v_CA01_1397"), 
                     level='DA', quiet=TRUE, geo_format='sf', labels='short')
#2006 bachelor degree 15-24, 25-64, 65+
CA06_bachelor=get_census(dataset='CA06', regions=list(CMA="35521"),
                         vectors=c("bachelor_degree_15_24"= 
                        "v_CA06_1243","bachelor_degree_25_64"= "v_CA06_1257",
                         "bachelor_degree_65"="v_CA06_1271"),
                     level='DA', quiet=TRUE, geo_format='sf', labels='short')
#2011 bachelor degree 15+, 25-64 
CA11_bachelor=get_census(dataset='CA11', regions=list(CMA="35521"),
                         vectors=c("bachelor_degree_15"="v_CA11N_1795", 
                                   "bachelor_degree_25_64"="v_CA11N_1825"), 
                     level='DA', quiet=TRUE, geo_format='sf', labels='short')
#2016 bachelor degree 15+, 25-64 
CA16_bachelor=get_census(dataset='CA16', regions=list(CMA="35521"),
                         vectors=c("bachelor_degree_15"="v_CA16_5081",
                                   "bachelor_degree_25_64"="v_CA16_5126"), 
                     level='DA', quiet=TRUE, geo_format='sf', labels='short')
#2021 bachelor degree 15+, 25-64 
CA21_bachelor=get_census(dataset='CA21', regions=list(CMA="35521"),
                         vectors=c("bachelor_degree_15"="v_CA21_5850",
                                   "bachelor_degree_25_64"="v_CA21_5898"), 
                     level='DA', quiet=TRUE, geo_format='sf', labels='short')
```

Since there are differences in between each census year (i.e., separation of bachelor degree holders within different age categories throughout time), I am going to amalgamate them since I personally don't really care what age category has more at this point in time, I just want to see if there is an overall change in bachelor degrees being held. \
Also, while plotting the number of bachelor degree holders over time is... interesting, it does not standardize nor account for the potential change in population influx over time. If we were to convert the number of degree holders per area over time into a percentage, it would be slightly more meaningful overtime to compare rates of change. 



```r
#2001 only has one category... so no need for lots of maths
CA01_bachelor=CA01_bachelor %>%                       #overwrite previous df
  clean_names() %>%                                   #clean col names
  mutate(percentage=round((bachelor_degree_higher     #create proportion %
                           /(population))*100, 2)) %>%#idk why this creates a new col??
  select(population, bachelor_degree_higher, percentage, everything()) %>% #arrange cols
  mutate(census_year="CA01")                          #create census year col
#2006 we have to do some *additional*... maths *ba boom ching*
CA06_bachelor=CA06_bachelor %>%
  clean_names() %>%
  rowwise() %>%                                     #need function to apply across
  mutate(bachelor_degree_total=sum(bachelor_degree_15_24,     #sum all degree ages 
                                   bachelor_degree_25_64, 
                                   bachelor_degree_65,
                                   na.rm=TRUE)) %>%
  mutate(percentage=round((bachelor_degree_total         #create new proportion %  
                           /(population))*100, 2)) %>%
  select(population, bachelor_degree_15_24, bachelor_degree_25_64, bachelor_degree_65, 
         bachelor_degree_total, percentage, everything()) %>%     
  mutate(census_year="CA06")                             
#2011 same thing as previous census year except here onwards things become more standard in their age categories 
CA11_bachelor=CA11_bachelor %>%
  clean_names() %>%
  rowwise() %>%                                     
  mutate(bachelor_degree_total=sum(bachelor_degree_15, bachelor_degree_25_64, 
                                   na.rm=TRUE)) %>%
  mutate(percentage=round((bachelor_degree_total          
                           /(population))*100, 2)) %>%
  select(population, bachelor_degree_15, bachelor_degree_25_64, bachelor_degree_total, 
         percentage, everything()) %>%
  mutate(census_year="CA11")
#2016...
CA16_bachelor=CA16_bachelor %>%
 clean_names() %>%
  rowwise() %>%                                     
  mutate(bachelor_degree_total=sum(bachelor_degree_15, bachelor_degree_25_64, 
                                   na.rm=TRUE)) %>%
  mutate(percentage=round((bachelor_degree_total          
                           /(population))*100, 2)) %>%
  select(population, bachelor_degree_15, bachelor_degree_25_64, bachelor_degree_total, 
         percentage, everything()) %>%
  mutate(census_year="CA16") 
#2021
CA21_bachelor=CA21_bachelor %>%
 clean_names() %>%
  rowwise() %>%                                     
  mutate(bachelor_degree_total=sum(bachelor_degree_15, bachelor_degree_25_64, 
                                   na.rm=TRUE)) %>%
  mutate(percentage=round((bachelor_degree_total          
                           /(population))*100, 2)) %>%
  select(population, bachelor_degree_15, bachelor_degree_25_64, bachelor_degree_total, 
         percentage, everything()) %>%
  mutate(census_year="CA21") 
```
  
 

```r
#the geographic region was too large with any of the other three CSD regions, rendering it EXTREMELY difficult to see anything above 40% (telling), so had to only keep the Kingston DAs.. oh well.
CA01_bachelor_fig=CA01_bachelor %>%
  filter(region_name=="Kingston") %>%  
#Generate the plot:  
  ggplot() +
  geom_sf(aes(fill=percentage)) +                       #make data plot by %
  scale_fill_distiller(name= "Bachelor Degree Holders %",
                       palette= "BuPu",
                       limits = c(0, 100)) +            #keep scale from 0-100%
#Plot aesthetics:  
  theme_minimal() +
  theme(plot.background=element_rect(fill="black"),        #make figure background dark
        panel.grid.major = element_line(colour="#262626")) + #colourlat long grid lines
  guides(fill=guide_colourbar(barwidth=10, barheight=0.5,    #edit key size here bc bar
                             direction="horizontal", title.position="top", 
                             title.hjust = 0.3)) +        
  theme(legend.position="bottom", legend.title=element_text(size=10, colour="white"), 
        legend.text=element_text(size=11, colour="white")) +     #edit legend themes    
#Generate labels:  
geom_text(x = -76.65, y=44.47, label="2001", colour="white", size=7)
  

print(CA01_bachelor_fig)
```

![](A2_kozak_files/figure-html/3.1.1.: Q1 CA01 Figure-1.png)<!-- -->


```r
CA06_bachelor_fig=CA06_bachelor %>%
  filter(region_name=="Kingston") %>%  
#Generate the plot:  
  ggplot() +
  geom_sf(aes(fill=percentage)) +                       #make data plot by %
  scale_fill_distiller(name= "Bachelor Degree Holders %",
                       palette= "BuPu",
                       limits = c(0, 100)) +            #keep scale from 0-100%
#Plot aesthetics:  
  theme_minimal() +
  theme(plot.background=element_rect(fill="black"),        #make figure background dark
        panel.grid.major = element_line(colour="#262626")) + #colourlat long grid lines
  guides(fill=guide_colourbar(barwidth=10, barheight=0.5,    #edit key size here bc bar
                             direction="horizontal", title.position="top", 
                             title.hjust = 0.3)) +        
  theme(legend.position="bottom", legend.title=element_text(size=10, colour="white"), 
        legend.text=element_text(size=11, colour="white")) +     #edit legend themes    
#Generate labels:  
geom_text(x = -76.65, y=44.47, label="2006", colour="white", size=7)
  

print(CA06_bachelor_fig)
```

![](A2_kozak_files/figure-html/3.1.1.: Q1 CA06 Figure-1.png)<!-- -->





*Side note: So, in 2021 they changed the way in which the `region_name` column was organized from simply just stating the name to a numeric form. I had to go back to the `CA21_ed_CSD` data frame where the `geo_uid` column corresponded to four values (3510005, 3510010, 3510020, 3511005) that were assigned to Frontenac Islands, Kingston, South Frontenac, and Loyal CSDs. Under the `csd_uid` column in `CA21_bachelor` there were only these four values returned, and moving forward I am assuming that these correspond to those locations. Everything appears right, so. Yay.*  





spotty because we don't know whether these are locals, Canadian residents, or immigrants who are contributing to these changes over time without also including that type of data.



```r
animation::saveGIF(
  expr = {
    par(pty = "s")  # Set plot aspect ratio to be square
    plot(CA01_bachelor_fig, width=4.5, height=4.5)
    plot(CA06_bachelor_fig, width=4.5, height=4.5)
    plot(CA11_bachelor_fig, width=4.5, height=4.5)
    plot(CA16_bachelor_fig, width=4.5, height=4.5)
    plot(CA21_bachelor_fig, width=4.5, height=4.5)
  },
  movie.name = "CA_bachelor_01_21_large.gif"
)
```

```
## Output at: CA_bachelor_01_21_large.gif
```

```
## [1] TRUE
```

Code to generate a GIF from separate plots was found from [here.](https://community.rstudio.com/t/combining-multiple-plots-into-animation-within-r-markdown/63181)


________________________________________________________________________________
## 4. Concluding Comments and Thoughts
#### 4.1 Notable Lessons from Mr. Wilke


#### 4.2 General Closing Comments: \
- The `cancensus` package information was extremely detailed and useful. It made figuring out how to access the data very clear and somewhat straightforward. So that was super nice and useful to have, especially since working with this data is truly... endless, wow. There are millions and millions of things to look at, compute, code and deign for. Great data set without any limitations (except API rates), but one would have to have a fairly clear picture or idea of where to go with the data. I personally started off waaaay too large of any overall analysis that I wanted to do, which made this assignment seem very overwhelming, so I had to dramatically scale back. /
- One thing I noticed was how frequently I switched from the Royal 'We' to the first person 'I' throughout the assignment. I do not know how to more readily combat this, so I apologize. /
- I felt like I was doing this assignment sometimes twice... once to meet the specified criteria, and a second time with a geographic location that I was familiar with (Winnipeg) to see what results were coming back so as to understand the equivalent for the city I didn't know. I feel like one would return something more thorough if they could work within whatever chosen region because the investigation would be more self explanatory. But alas, challenges of 'unknown' data.   



________________________________________________________________________________
## 5. Appendix

Here, the five census plots for bachelor degree types are located as stills for viewing.


```r
#the geographic region was too large with any of the other three CSD regions, rendering it EXTREMELY difficult to see anything above 40% (telling), so had to only keep the Kingston DAs.. oh well.
CA01_bachelor_fig=CA01_bachelor %>%
  filter(region_name=="Kingston") %>%  
#Generate the plot:  
  ggplot() +
  geom_sf(aes(fill=percentage)) +                       #make data plot by %
  scale_fill_distiller(name= "Bachelor Degree Holders %",
                       palette= "BuPu",
                       limits = c(0, 100)) +            #keep scale from 0-100%
#Plot aesthetics:  
  theme_minimal() +
  theme(plot.background=element_rect(fill="black"),        #make figure background dark
        panel.grid.major = element_line(colour="#262626")) + #colourlat long grid lines
  guides(fill=guide_colourbar(barwidth=10, barheight=0.5,    #edit key size here bc bar
                             direction="horizontal", title.position="top", 
                             title.hjust = 0.3)) +        
  theme(legend.position="bottom", legend.title=element_text(size=10, colour="white"), 
        legend.text=element_text(size=11, colour="white")) +     #edit legend themes    
#Generate labels:  
geom_text(x = -76.65, y=44.47, label="2001", colour="white", size=7)
  

print(CA01_bachelor_fig)
```

![](A2_kozak_files/figure-html/3.1.1.: Q1 CA01 Figure A-1.png)<!-- -->


```r
CA06_bachelor_fig=CA06_bachelor %>%
  filter(region_name=="Kingston") %>%  
#Generate the plot:  
  ggplot() +
  geom_sf(aes(fill=percentage)) +                       #make data plot by %
  scale_fill_distiller(name= "Bachelor Degree Holders %",
                       palette= "BuPu",
                       limits = c(0, 100)) +            #keep scale from 0-100%
#Plot aesthetics:  
  theme_minimal() +
  theme(plot.background=element_rect(fill="black"),        #make figure background dark
        panel.grid.major = element_line(colour="#262626")) + #colourlat long grid lines
  guides(fill=guide_colourbar(barwidth=10, barheight=0.5,    #edit key size here bc bar
                             direction="horizontal", title.position="top", 
                             title.hjust = 0.3)) +        
  theme(legend.position="bottom", legend.title=element_text(size=10, colour="white"), 
        legend.text=element_text(size=11, colour="white")) +     #edit legend themes    
#Generate labels:  
geom_text(x = -76.65, y=44.47, label="2006", colour="white", size=7)
  

print(CA06_bachelor_fig)
```

![](A2_kozak_files/figure-html/3.1.1.: Q1 CA06 Figure A-1.png)<!-- -->


```r
CA11_bachelor_fig=CA11_bachelor %>%
  filter(region_name=="Kingston") %>%  
#Generate the plot:  
  ggplot() +
  geom_sf(aes(fill=percentage)) +                       #make data plot by %
  scale_fill_distiller(name= "Bachelor Degree Holders %",
                       palette= "BuPu",
                       limits = c(0, 100)) +            #keep scale from 0-100%
#Plot aesthetics:  
  theme_minimal() +
  theme(plot.background=element_rect(fill="black"),        #make figure background dark
        panel.grid.major = element_line(colour="#262626")) + #colourlat long grid lines
  guides(fill=guide_colourbar(barwidth=10, barheight=0.5,    #edit key size here bc bar
                             direction="horizontal", title.position="top", 
                             title.hjust = 0.3)) +        
  theme(legend.position="bottom", legend.title=element_text(size=10, colour="white"), 
        legend.text=element_text(size=11, colour="white")) +     #edit legend themes    
#Generate labels:  
geom_text(x = -76.65, y=44.47, label="2011", colour="white", size=7)
  

print(CA11_bachelor_fig)
```

![](A2_kozak_files/figure-html/3.1.1.: Q1 CA11 Figure A-1.png)<!-- -->


```r
CA16_bachelor_fig=CA16_bachelor %>%
  filter(region_name=="Kingston") %>%  
#Generate the plot:  
  ggplot() +
  geom_sf(aes(fill=percentage)) +                       #make data plot by %
  scale_fill_distiller(name= "Bachelor Degree Holders %",
                       palette= "BuPu",
                       limits = c(0, 100)) +            #keep scale from 0-100%
#Plot aesthetics:  
  theme_minimal() +
  theme(plot.background=element_rect(fill="black"),        #make figure background dark
        panel.grid.major = element_line(colour="#262626")) + #colourlat long grid lines
  guides(fill=guide_colourbar(barwidth=10, barheight=0.5,    #edit key size here bc bar
                             direction="horizontal", title.position="top", 
                             title.hjust = 0.3)) +        
  theme(legend.position="bottom", legend.title=element_text(size=10, colour="white"), 
        legend.text=element_text(size=11, colour="white")) +     #edit legend themes    
#Generate labels:  
geom_text(x = -76.65, y=44.47, label="2016", colour="white", size=7)
  

print(CA16_bachelor_fig)
```

![](A2_kozak_files/figure-html/3.1.1.: Q1 CA16 Figure A-1.png)<!-- -->

*Side note: So, in 2021 they changed the way in which the `region_name` column was organized from simply just stating the name to a numeric form. I had to go back to the `CA21_ed_CSD` data frame where the `geo_uid` column corresponded to four values (3510005, 3510010, 3510020, 3511005) that were assigned to Frontenac Islands, Kingston, South Frontenac, and Loyal CSDs. Under the `csd_uid` column in `CA21_bachelor` there were only these four values returned, and moving forward I am assuming that these correspond to those locations. Everything appears right, so. Yay.*  


```r
#`region_name` changes here from the actual alphabetic name to numeric codes...
CA21_bachelor_fig=CA21_bachelor %>%
  filter(csd_uid=="3510010") %>%  
#Generate the plot:  
  ggplot() +
  geom_sf(aes(fill=percentage)) +                       #make data plot by %
  scale_fill_distiller(name= "Bachelor Degree Holders %",
                       palette= "BuPu",
                       limits = c(0, 100)) +            #keep scale from 0-100%
#Plot aesthetics:  
  theme_minimal() +
  theme(plot.background=element_rect(fill="black"),        #make figure background dark
        panel.grid.major = element_line(colour="#262626")) + #colourlat long grid lines
  guides(fill=guide_colourbar(barwidth=10, barheight=0.5,    #edit key size here bc bar
                             direction="horizontal", title.position="top", 
                             title.hjust = 0.3)) +        
  theme(legend.position="bottom", legend.title=element_text(size=10, colour="white"), 
        legend.text=element_text(size=11, colour="white")) +     #edit legend themes    
#Generate labels:  
geom_text(x = -76.65, y=44.47, label="2021", colour="white", size=7)
  

print(CA21_bachelor_fig)
```

![](A2_kozak_files/figure-html/3.1.1.: Q1 CA21 Figure A-1.png)<!-- -->



