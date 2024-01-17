# Democratizing Chicago's Menu Money Data
Parsing, cleaning, and categorizing Chicago's menu money expenditures.

# Description
Each year, every member of Chicago's city council  gets $1.5 million in "menu money"  to spend on capital improvement in their ward. Some alderpersons spend this money on streets and sidewalks, while others use it to fund park upkeep, bike lanes, or art projects. The alderperson has enormous discretion over how this money gets spent -- and, in the past, menu money has been at the center of [bribery and corruption scandals](https://www.chicagobusiness.com/government/chicago-ald-carrie-austin-indicted-bribery-charges). Yet the city only publishes menu money expenditures in massive, unsightly, and inconsistently organized PDFs buried in the city website. Because of this inaccessibility, it has historically been difficult to scrutinize how menu money is actually spent. 

This project aims to change that, by making the data public in a simple spreadsheet and sorting each line item into one of nine broad categories. The bigger goal: to empower the public to finally understand how their tax dollars are being spent, allow reporters to sniff out suspicious patterns, and help social scientists study how dollars get allocated at the hyper-micro level.

# Getting Started
If you just want the final cleaned and categorized data, you'll find that in the [AllMenu2012-2023](AllMenu2012-2023.csv) file. Enjoy!

If you want to run or play with the code, you'll need R/R Studio with the following packages installed: 
- stringr
- pdftools
- tidyverse
- dplyr

# Methodology
## Cleaning and Categorization 
[Expenditure PDFs](menu-money-pdfs/) from each menu money year are read in and the necessary lines are subsetted and retained based on the architecture of the PDF from that year. All years are appended together. For each of the nine categories, a list of preliminary terms most commonly associated with the category is defined (e.g., "street," "alley," "resurfacing," etc. for Streets & Transportation; "artwork," "sculpture," "fountain," etc. for Arts & Beautification). Each line item is then preliminarily categorized by searching for any of those terms in the cleaned 'program' or 'description' columns of the PDF-derived data. 

## Data Validation and Correction
To ensure accuracy, the totals for each ward-year are computed and compared to the totals listed in the original PDFs. The sources of any discrepancies are identified and corrected on a case-by-case basis. Next, programs with a small number of line-items are reviewed to ensure they have been categorized correctly. For each category, a new list of line item-specific "hand correction" terms is defined which are then used to override original classifications where necessary. After the reclassification is complete, final ward-year totals are reviewed again to ensure that they match the PDFs. Finally, for each menu money year in the dataset two wards were randomly selected for a line-by-line manual review.

## Categorization Strategy
Because many expenditures could reasonably be placed in several categories, the following hierarchy was applied in category determinations to ensure consistency: 
1. Security Cameras
2. Schools & Libraries
3. Parks & Recreation
4. Beautification
5. Streets & Transportation
6. Lighting 
7. Plants, Gardens, & Sustainability
8. Bike Infrastructure
9. Miscellaneous

In other words, a security camera placed in a park was categorized as "Security Cameras" rather than "Parks & Recreation." Upkeep to a school playground is categorized as "Schools & Libraries" rather than "Parks & Recreation"; similarly, a mural in a school building is categorized as "Schools & Libraries" rather than "Beautification." A traffic light is always categorized as "Streets & Transportation" rather than "Lighting."

# What Do I Do With This?
Whatever you like. But here are a few ideas for projects that could really make the most of this data:
- An interactive web app where users can select their ward and see how their menu money was spent last year  
- Mapping expenditures by category (which might require cleaning up and geocoding addresses)
- Investigating whether participatory budgeting has actually changed how wards spend money (and, if so, how)

# Credits
[Jake J. Smith](http://www.jakejsmith.com) developed the code and methodology. Special thanks to [Andres Fonseca](https://github.com/fonsecaa) for critical early contributions and for supporting a tiny widdle baby R user.
