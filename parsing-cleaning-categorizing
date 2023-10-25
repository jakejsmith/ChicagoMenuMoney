# PDFs detailing annual menu money allocation for 2012-2012 were obtained from City of Chicago OBM Capital Improvement Program website (https://www.chicago.gov/city/en/depts/obm/provdrs/cap_improve/svcs/cip-archive.html). PDFs must be stored in directory with file names formatted as "20XXMenu.pdf."

library(stringr)
library(pdftools)
library(tidyverse)
library(dplyr)

setwd("C:\\Users\\jacosmit\\Desktop\\jake\\menumoney\\PDFs")
options(scipen=50, digits = 2)

# Cleaning 2012 to 2016 ====
for (year in 2012:2016){
  menu <- pdf_text(paste0(year,"Menu.pdf")) %>% readr::read_lines()
  menu_keep <- menu[grep("Ward|\\$|Program", menu)] 

  cutsA <- grep("Menu Detail|Total:|\\$0.00|Year", menu_keep) 
  cutsB <- grep("Ward Balance|Menu Budget|Committed", menu_keep) 
  cutsC <- cutsB + 1
  menu_sub <- menu_keep[-c(cutsA, cutsB, cutsC)]
  
  text <- str_squish(menu_sub) %>% 
    str_replace(paste(year, " Ward : "), "Ward: ")
  df <- data.frame(text) 
  
  df$ward <- apply(df, 1, function(x) ifelse(grepl("Ward : ", x['text']), gsub(".* ([0-9]+$)", "\\1", x['text']), NA))
  df$program <- apply(df, 1, function(x) ifelse(grepl("Program : ",  x['text']), x['text'], NA))
  
  df <- df %>% 
    fill(ward) %>% 
    mutate(program = sub("Program : ", "", df$program),
           year = year) %>% 
    fill(program) %>% 
    filter(!grepl("Ward : ", text) & !grepl("Program", text)) %>% 
    separate(text, c("description", "costA", "costB"), sep="\\$") %>% 
    filter(!is.na(costA)) %>% 
    mutate(
      cost = as.numeric(gsub(",", "", ifelse(is.na(costB), costA, costB)))
    )

  write.csv(df, file = paste0("Menu", year, ".csv"))
  assign(paste0("menu", year), df)
}

# Cleaning 2017 to 2023 ====
for (year in 2017:2023){
  menu <- pdf_text(paste0(year,"Menu.pdf")) %>% readr::read_lines()
  menu_keep <- menu[grep("Ward|\\$", menu)] 
  
  cuts <- grep("MENU BUDGET|WARD COMMITTED [0-9]* TOTAL|\\$0.00|WARD [0-9]* BALANCE|MenuPackage|Menu Ward Detail Report", menu_keep) 
  df <- menu_keep[-cuts] %>% data.frame()
    df <- rename(df, text = .)
  
  df$ward <- apply(df, 1, function(x) ifelse(grepl("Ward: ", x['text']), gsub(".* ([0-9]+$)", "\\1", x['text']), NA))
  
  df <- df %>% 
    fill(ward) %>% 
    filter(!grepl("Ward: ", text)) %>% 
    separate(text, c("program", "description", "costA"), sep = "  +") %>% 
    mutate(
      year = year,
      program = sub(paste0("\\(", year, "\\)"), "", program),
      costA = ifelse(is.na(costA) & grepl("\\$", description), description, costA)) %>% 
    mutate(
      cost = ifelse(grepl("\\.", costA), 
                    as.numeric(gsub(",", "", gsub("\\$", "", gsub("\\.", "", costA))))/100,
                    as.numeric(gsub(",", "", gsub("\\$", "", gsub("\\.", "", costA))))),
      costB = NA
    )

  df <- df %>% 
    mutate(program = ifelse(
      program %in% c("Signs Menu ", "Menu ", ""), 
        case_when(
          program == "Signs Menu " ~ "In-Road State Law Stop For Pedestrians Signs",
          program == "Menu " ~ "Street Light Residential Staggered Piggy Back",
          program == "" ~ "In-Road State Law Stop For Pedestrians Signs"
        ),
      program
    ))
  
  write.csv(df, file = paste0("Menu", year, ".csv"))
  assign(paste0("menu", year), df)
}

# APPEND ALL YEARS  ====
allMenu_list <- lapply(2012:2023, function(x) bind_rows(get(paste0("menu", x)))) 
allMenu <- do.call("rbind", allMenu_list)

allMenu$ward <- as.numeric(allMenu$ward)

# CLASSIFICATION ==== 
streetwords <- c("street","alley","apron","resurfacing","sidewalk","countdown","traffic","pedestr","sign","gutter","curb","signal","pavement","bollard","arrow","parking","bus", "highway","cul-de-sac","guardrail","avenue", "ramp", "median", "road ", "CTA", " bridge", "CDOT")
lightwords <- c("light", " pole ", "flashing beacon", "LED ")
beautwords <- c("arts", "artwork", "art pro", "sculpture", "fountain", "fountian", "mural", "painting", "power wash", "stamped crosswalk", "DCASE", "pond", "landscap", "model block", "underpass", "viaduct improvement", "fence", "public art", "decorative", "monument", "CPAG", "Community Identifier")
parkwords <- c("play", "playground", " field", "improvements", "parks ", "dog", "trail", "bench", "basketball", "baseball", "plaground", "partnership", "stadium")
camwords <- c("camera", "POD ", "PTZ", "LPR")
schoolwords <- c("library", "reading", "school", "study", "learning", "high school", "elementary", "CPS")
gardenwords <- c("trees", "tree ", "planter", "flower", "rain barrel", "garden", "planting", "green space", "NeighborSpace")
bikewords <- c("bike lane", "bike station", "bike rack", "bike", "greenway")

allMenu <- mutate(
  allMenu, 
  category = case_when(
    grepl(paste(camwords, collapse="|"), allMenu$program, ignore.case = TRUE) ~ "Security Cameras",
    grepl(paste(beautwords, collapse="|"), allMenu$program, ignore.case = TRUE) ~ "Beautification",
    grepl(paste(parkwords, collapse="|"), allMenu$program, ignore.case = TRUE) ~ "Parks & Recreation",
    grepl(paste(lightwords, collapse="|"), allMenu$program, ignore.case = TRUE) ~ "Lighting",
    grepl(paste(streetwords, collapse="|"), allMenu$program, ignore.case = TRUE) ~ "Streets & Transportation",
    grepl(paste(schoolwords, collapse="|"), allMenu$program, ignore.case = TRUE) ~ "Schools & Libraries",
    grepl(paste(bikewords, collapse="|"), allMenu$program, ignore.case = TRUE) ~ "Bike Infrastructure",
  grepl(paste(gardenwords, collapse="|"), allMenu$program, ignore.case = TRUE) ~ "Plants, Gardens, & Sustainability")
)

## Fix categories where the description ended up in "description" instead of "program"
allMenu <- mutate(
  allMenu, 
    category = case_when(
      grepl(paste(beautwords, collapse="|"), description, ignore.case = TRUE) ~ "Beautification",
      grepl(paste(parkwords, collapse="|"), description, ignore.case = TRUE) ~ "Parks & Recreation",
      grepl(paste(lightwords, collapse="|"), description, ignore.case = TRUE) ~ "Lighting",
      grepl(paste(streetwords, collapse="|"), description, ignore.case = TRUE) ~ "Streets & Transportation",
      grepl(paste(camwords, collapse="|"), description, ignore.case = TRUE) ~ "Security Cameras",
      grepl(paste(schoolwords, collapse="|"), description, ignore.case = TRUE) ~ "Schools & Libraries",
      grepl(paste(gardenwords, collapse="|"), description, ignore.case = TRUE) ~ "Plants, Gardens, & Sustainability",
      grepl(paste(bikewords, collapse="|"), description, ignore.case = TRUE) ~ "Bike Infrastructure",
      !is.na(category) ~ category
    ))

allMenu$category[is.na(allMenu$category)] <- "Miscellaneous"

# DATA VALIDATION AND ADJUSTMENTS  ====

# Compare ward spending totals for each year to totals on PDFs
allMenu %>% group_by(year, ward) %>% 
  summarize(total = sum(cost, na.rm = T)) %>% arrange(ward) %>% view(title = "Annual Ward Totals - PRELIMINARY")
  
  # 2012 hand corrections
  allMenu$cost[allMenu$ward == 31 & 
                 allMenu$year == 2012 & 
                 allMenu$program == "High Definition Camera"] <- 17250
  
  # 2013 hand corrections
  allMenu$cost[allMenu$ward == 23 & 
                 allMenu$year == 2013 & 
                 allMenu$program == "Chicago Park District" &
                 is.na(allMenu$cost)] <- 154112
  
  allMenu$cost[allMenu$ward == 25 & 
                 allMenu$year == 2013 & 
                 allMenu$program == "Chicago Park District" &
                 is.na(allMenu$cost)] <- 360276
  
  # 2015 note: Wards 1 and 2 give their committed totals as $1.32M; however, adding up the line items gives a different total for each ward. On closer inspection, Ward 1 is over-budget by exactly the amount that Ward 2 is under-budget, and notes in the PDF indicate there is cost-sharing for certain projects. In this case, the discrepancy in annual totals in the PDF vs. the derived dataset is not an error, but appears to be a book-keeping quirk.
  
  # 2017 hand-corrections
  missing_obs <- data.frame("2235 E 95TH ST", "", "", 7, "Alley Apron Menu", 2017, 10554.3, "Streets & Transportation")
  names(missing_obs) <- names(allMenu)
  allMenu <- rbind(allMenu, missing_obs)
  
  # 2018 hand-corrections
  allMenu$cost[allMenu$ward == 1 & 
              allMenu$program == "In-Road State Law Stop For Pedestrians Signs" & 
              is.na(allMenu$cost)] <- 350
  
  allMenu$cost[allMenu$ward == 2 & 
                 allMenu$program == "In-Road State Law Stop For Pedestrians Signs" & 
                 is.na(allMenu$cost)] <- 350
  
  missing_obs <- data.frame("ON S CHRISTIANA AVE FROM W 108TH ST (10800 S) TO W 109TH ST (10900 S)", "", "", 19, "Street Resurfacing Menu", 2018, 207.45, "Streets & Transportation")
  names(missing_obs) <- names(allMenu)
  allMenu <- rbind(allMenu, missing_obs)
  
  missing_obs <- data.frame("WOLCOTT AVE", "", "", 47, "Concrete Alley Menu", 2018, 34568, "Streets & Transportation")
  names(missing_obs) <- names(allMenu)
  allMenu <- rbind(allMenu, missing_obs)
  
  missing_obs <- data.frame("5052 N WINTHROP AVE", "", "", 48, "Street Light Pole for High Definition Camera Menu", 2018, 27.26, "Security Cameras")
  names(missing_obs) <- names(allMenu)
  allMenu <- rbind(allMenu, missing_obs)
  
  # Note: No discrepancies in yearly ward totals in 2019-2022
  
# Identify programs with small number of line items; confirm these are not errors, and re-categorize as needed.
allMenu %>% count(program, category) %>% arrange(n) %>% view(title = "Program Counts")

  allMenu$category[allMenu$program == "Art"] <- "Beautification"
  allMenu$category[allMenu$program == "Avenue"] <- "Parks & Recreation"
    # Full desc: "Dog Friendly Area - Lincoln Park at Bryn Mawr Avenue"
  allMenu$category[allMenu$program == "INDIANA"] <- "Security Cameras"
    # Full desc: "POD CAMERA RELOCATION - 69TH STREET & INDIANA"
  allMenu$category[allMenu$program == "lot"] <- "Schools & Libraries"
    # Full desc: "Little Village Academy - Athletic Field and Play lot"
  allMenu$category[allMenu$program == "Menu"] <- "Parks & Recreation"
    # Full desc: "Lawler Park Truf Field 2018/2019/2020 Menu"
  allMenu$category[allMenu$program == "Menu (2017)"] <- "Lighting"
    # Full desc: "Street Light Residential Staggered Piggy Back Menu (2017)"
  allMenu$category[allMenu$program == "Menu (2018)"] <- "Lighting"
    # Full desc: "Street Light Residential Staggered Piggy Back Menu (2018)"
  allMenu$category[allMenu$program == "Miscellaneous CDOT Projects " & 
                   allMenu$category == "Security Cameras"] <- "Streets & Transportation"
  allMenu$category[allMenu$program == "CULLERTON ST." & 
                   allMenu$cost == 27011] <- "Security Cameras"

  # Note: For efficiency, category corrections are grouped together in the "hand-correction" groups below whenever possible, using unique identifier terms

# Identify line items with missing cost data; cross-reference with PDF to fix or delete as appropriate
allMenu %>% filter(is.na(cost)) %>% view(title = "Cost NA")
  
  allMenu <- allMenu %>% filter(!grepl("20,941)", costA))
  # Part of a line that is already accounted for elsewhere
  
  allMenu <- allMenu %>% filter(!grepl("Shabbona Park \\(TPC ", description))
  # Part of a line that is already accounted for elsewhere
  
  allMenu <- allMenu %>% filter(!grepl("Fire alarm", description))
    # This was not a new line-item, but rather a note justifying the cost for a line-item
  
  allMenu <- allMenu %>% filter(!grepl("Gateway; & Various Other 20th Ward Public", program))
  # Part of a line that is already accounted for elsewhere
  
  allMenu <- allMenu %>% filter(!grepl("Cost Split in Half", program))
  # Part of a line that is already accounted for elsewhere
  
  allMenu <- allMenu %>% filter(!grepl("29th Ward Partnership", program))
  # Part of a line that is already accounted for elsewhere
  
  allMenu <- allMenu %>% filter(!grepl("W. 31st St.", program))
  # Part of a line that is already accounted for elsewhere
  
  allMenu <- allMenu %>% 
    mutate(
      program = 
        ifelse(cost == 154406 & ward == 11 & year == 2022,
               "Design and installation of left turn arrows at S. Halsted St. & W. 31st St. (Ward 11)",
               program)
    )
  # Fixing a miscategorized item related to the removed line above
      
## Define hand-corrections for individual observations, from cross-ref w/ orig documents
handcorrections_light <- c(
  "Street Light"
)

handcorrections_street <- c(
  "Alley Apron",
  "bollard menu",
  "bus stop",
  "crossing light",
  "Concrete Alley Menu",
  "curb & gutter",
  "Green Street Improvements and Repairs",
  "traffic diverter",
  "bus benches",
  "Pavement Markings",
  "Alley Speed Hump Menu",
  "Alley Resurfacing",
  "Street Resurfacing",
  "sidewalk",
  "Street Speed Hump",
  "State Law Stop",
  "traffic signal",
  "northbound Loyola stations"
)

handcorrections_park <- c(
 "Tennis Court",
  "2017 and 2018 Menu", # Desc: "Wentworth Park - Athletic Field Lighting - 2017 and 2018 Menu",
 "Austin Town Hall Park",
 "Bauler Park",
 "Bell Park",
 "Blackhawk Park",
 "Broadway Armory Park",  
 "Chamberlain Triangle Park",
 "Chicago Park District",
 "Clover Park",
 "Conners Park",
 "Connors Park",
 "Donovan Park",
 "Douglas Park",
 "Drinking Fountains",
 "Dubkin Park",
 "Emmerson Park",
 "Gladstone Park",
 "Grandparent's Park",
 "Haas Park",
 "Hiawatha Park",
 "Horner Park",
 "Kelly Park",
 "Ken-Well Park",
 "Kenwood Park",
 "Kosciuszko Park",
 "Lakeview Low-Line Park",
 "Lake Shore Park Irrigation System",
 "Lake Shore Park Outdoor Fitness Center",
 "LaPointe Park",
 "Lawler Park",
 "Leone Beach",
 "Lincoln Park Pathway Installation",
 "Loyola Park",
 "Marquette Park",
 "Montgomery Ward Park",
 "Montrose Beach",
 "Munroe Park",
 "Oriole Park",
 "Paul Revere Park", 
 "Pottawatomie, Touhy, and Sherwin Parks",
 "Roosevelt Park",
 "Rowan Park",
 "Sam Leone Park",
 "Sauganash Park",
 "Senn Park",
 "Sherwin Parks",
 "Touhy Park",
 "Trebes Park",
 "Triangle Park",
 "Vittum Park",
 "Welles Park",
 "Willye White Park"
  )

handcorrections_cam <- c(
  "camera",
  "1 POD Camera: Streeter & Illinois",
  "5021 W FULLERTON AVE",
  "& 71st & Champlain",
  "1532 W FULTON ST",
  "S BLACKSTONE AVE & E 53RD ST:S VINCENNES AVE & E 37TH ST",
  "Peshtigo Ct, Grand & McClurg",
  
  "cancelled" # Full desc: "POD Cameras - 3 locations (47th & Vincennes cancelled)"
      # Note: Line item still lists $81,033 cost, suggesting cost was not actually cancelled.
  ) 

handcorrections_beaut <- c(
  "29th Ward Partnership", # Full desc: "Mural at N. Central Avenue and W Lake Street w/ DCASE - 29th Ward Partnership"
  "project w/ 39th Ward", # Full desc: "Forest Glen viaduct lettering - partnership project w/ 39th Ward"
  "Gateway; & Various Other 20th Ward Public", # Full desc: "Gateway; & Various Other 20th Ward Public Art Projects"
  "Viaduct Improvement Menu",
  "Division Street Gateways"
  )
  
handcorrections_school <- c(
  "Library",
  "Chicago Public Schools",
  "48th Ward School Improvements",
  "Blair School",
  "Bridge Elementary School",
  "Campus Park",
  "Chicago Academy",
  "Clemente High School",
  "CPS",
  "Disney II",
  "elementary",
  "Goudy School",
  "Hurley School",
  "LaSalle II School Playground",
  "Lane Tech",
  "Nixon School",
  "Poe School",
  "Pritzker Elementary - Playground Renovation",
  "Prosser Academy",
  "Pulaski Elementary",
  "Reinberg Elementary",
  "Sayre Language Academy",
  "Senn High School",
  "Steinmetz College Prep",
  "School Projects",
  "School Murals",
  "Washington/Irving School",
  "Carpet Replacement" # only instance is a carpet replacement at library
  )

handcorrections_garden <- c(
  "NeighborSpace",
  "Planter Installation On Fullerton Avenue",
  "planter boxes",
  "Tree Planting in Various Locations",
  "Tree planting tree planting"
  )

handcorrections_bike <- c("bike lane")

handcorrections_misc <- c(
  "Honorary Sign",
  "Facility Improvements Engine House 79",
  "N Sheffield from W Armitage Ave to W Webster"
  )

allMenu <- allMenu %>% 
  mutate(
    category = ifelse(grepl(paste(handcorrections_beaut, collapse="|"), program, ignore.case = TRUE), "Beautification", category),
    category = ifelse(grepl(paste(handcorrections_park, collapse="|"), program, ignore.case = TRUE), "Parks & Recreation", category),
    category = ifelse(grepl(paste(handcorrections_light, collapse="|"), program, ignore.case = TRUE), "Lighting", category),
    category = ifelse(grepl(paste(handcorrections_cam, collapse="|"), program, ignore.case = TRUE), "Security Cameras", category),
    category = ifelse(grepl(paste(handcorrections_street, collapse="|"), program, ignore.case = TRUE), "Streets & Transportation", category),
    category = ifelse(grepl(paste(handcorrections_school, collapse="|"), program, ignore.case = TRUE), "Schools & Libraries", category),
    category = ifelse(grepl(paste(handcorrections_bike, collapse="|"), program, ignore.case = TRUE), "Bike Infrastructure", category),
    category = ifelse(grepl(paste(handcorrections_misc, collapse="|"), program, ignore.case = TRUE), "Miscellaneous", category),
    
    category = ifelse(grepl(paste(handcorrections_beaut, collapse="|"), description, ignore.case = TRUE), "Beautification", category),
    category = ifelse(grepl(paste(handcorrections_park, collapse="|"), description, ignore.case = TRUE), "Parks & Recreation", category),
    category = ifelse(grepl(paste(handcorrections_light, collapse="|"), description, ignore.case = TRUE), "Lighting", category),
    category = ifelse(grepl(paste(handcorrections_cam, collapse="|"), description, ignore.case = TRUE), "Security Cameras", category),
    category = ifelse(grepl(paste(handcorrections_street, collapse="|"), description, ignore.case = TRUE), "Streets & Transportation", category),
    category = ifelse(grepl(paste(handcorrections_school, collapse="|"), description, ignore.case = TRUE), "Schools & Libraries", category),
    category = ifelse(grepl(paste(handcorrections_garden, collapse="|"), description, ignore.case = TRUE), "Plants, Gardens, & Sustainability", category),
    category = ifelse(grepl(paste(handcorrections_bike, collapse="|"), description, ignore.case = TRUE), "Bike Infrastructure", category),
    category = ifelse(grepl(paste(handcorrections_misc, collapse="|"), description, ignore.case = TRUE), "Miscellaneous", category)
  )

allMenu <- filter(allMenu, !is.na(cost) &
                    ward != 99) %>% 
  select(-costA, -costB) %>% 
  relocate(year, ward, cost, category, program, description) %>% 
  arrange(year, ward)
    
# Final check to confirm annual ward totals add up to amounts in PDFs
allMenu %>% group_by(year, ward) %>% 
  summarize(total = sum(cost, na.rm = T)) %>% arrange(year) %>% view(title = "Annual Ward Totals - FINAL")
  # Shows mismatch in Wards 1 and 2 in 2015. However, this is not a coding mistake; the amounts correspond to what is in the spreadsheet. For instance, the numbers for Ward 1 in the PDF add up to $1,344,404, not to $1,320,000 as stated in the "TOTAL COMMITTED" row. 

write.csv(allMenu, file = "AllMenu2012-2023.csv")
