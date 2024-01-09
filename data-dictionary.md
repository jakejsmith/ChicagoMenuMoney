# Data Dictionary for Chicago Menu Money Expenditures, 2012-2023
**year**
: The fiscal year of menu money appropriations from which a line-item expenditure is drawn. (In 2012-2020 each ward received $1.32M; starting in 2021, the amount per ward increased to $1.5M.)

**ward**
: The ward from whose menu money appropriations a line-item expenditure is drawn. (More information on Chicago wards, including ward maps and the name of the alderperson currently representing each ward, is [available here](https://www.chicago.gov/city/en/about/wards.html).)

**cost**
: The esetimated cost of the line-item according to Chicago Department of Transportation (CDOT) menu money documents.

**category**
: A general classification assigned to each line-item as part of the cleaning and categorization process. The following categories are used: Beautification; Bike Infrastructure; Lighting; Miscellaneous; Parks & Recreation; Plants, Gardens, & Sustainability; Schools & Libraries; Streets & Transportation; Security Cameras. (More information on the categorization hierarchy can be found in the project [README](README.md).)

**program**
: The menu "program" assigned to the line-item by the CDOT. This most commonly refers to one of the options on the CDOT "menu" that alderpersons have historically chosen from when allocating menu money (e.g., Street Resurfacing, Street Light Upgrades, Curb & Gutter, etc.). For less common expenditure types, however, this column may give a more detailed description, possibly including the location of the project (e.g., "Chicago Park District North Pond Restoration" or "1 POD Camera: 1801 W Armitage").

**description**
: The additional information provided by CDOT. This column most commonly provides location information about the line-item (either a block, an exact address, or a general location name). In some cases, it also provides additional project details. 
