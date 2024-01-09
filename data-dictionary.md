# Data Dictionary for Chicago Menu Money Expenditures, 2012-2023

![image](https://github.com/jakejsmith/ChicagoMenuMoney/assets/79525163/4e55a7d0-f6e5-40aa-b0c9-48aeed61145f)

**year**
: The fiscal year of menu money appropriations from which a line-item expenditure is drawn. (In 2012-2020 each ward received $1.32M per year; starting in 2021, the amount increased to $1.5M per ward per year.)

**ward**
: The ward from whose menu money appropriations a line-item expenditure is drawn. (More information on Chicago wards, including ward maps and the name of the alderperson currently representing each ward, is [available here](https://www.chicago.gov/city/en/about/wards.html).)

**cost**
: The estimated cost of the line-item according to original Chicago Department of Transportation (CDOT) budget documents.

**category**
: A general classification I assigned to each expenditure as part of my cleaning and categorization process. Each line-item is assigned to one of the following categories: Beautification; Bike Infrastructure; Lighting; Miscellaneous; Parks & Recreation; Plants, Gardens, & Sustainability; Schools & Libraries; Streets & Transportation; or Security Cameras. (More information on the categorization hierarchy can be found in the project [README](README.md).)

**program**
: The menu "program" assigned to the line-item by the CDOT. Most of the time this refers to one of the options on the CDOT "menu" that alderpersons have historically been presented when allocating menu money. That menu includes common expenditures like Street Resurfacing, Street Light Upgrades, Curb & Gutter, etc. For less common expenditure types, however, this column may give a more detailed description, possibly including the location of the project (e.g., "Chicago Park District North Pond Restoration" or "1 POD Camera: 1801 W Armitage").

**description**
: Any additional information provided by CDOT. This column most commonly provides location information about the line-item (either a block, an exact address, or a general location name). In some cases, it also provides additional project details. In some cases, no description or location is provided. in which case this field is blank.
