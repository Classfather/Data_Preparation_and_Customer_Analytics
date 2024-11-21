# Data_Preparation_and_Customer_Analytics
Analyze customer purchasing behavior, focusing on chip purchases, to provide actionable insights. Includes data preparation, feature engineering, customer segmentation, and strategic recommendations. Deliverables include analysis, insights, and reproducible R scripts for data-driven decision-making.

> #Load Libraries
> library(data.table)
data.table 1.16.2 using 2 threads (see ?getDTthreads).  Latest news: r-datatable.com

Attaching package: ‘data.table’

The following object is masked _by_ ‘.GlobalEnv’:

    .N

> library(ggplot2)
> library(ggmosaic)
> library(readr)
> library(stringr)
> 
> 
> #load data
> filePath <- "../Documents/"
> CustomerData <- fread(paste0(filePath, "QVI_purchase_behaviour.csv"))
> library(readxl)
> transactionData = read_excel("../desktop/QVI_transaction_data.xlsx")

-
/
                                                                     

/
                                                                     

-
                                                                     
> setDT(transactionData)
> 
> ##Exploratory data analysis
> head(transactionData)
    DATE STORE_NBR LYLTY_CARD_NBR TXN_ID PROD_NBR
   <num>     <num>          <num>  <num>    <num>
1: 43390         1           1000      1        5
2: 43599         1           1307    348       66
3: 43605         1           1343    383       61
4: 43329         2           2373    974       69
5: 43330         2           2426   1038      108
6: 43604         4           4074   2982       57
                                  PROD_NAME PROD_QTY TOT_SALES
                                     <char>    <num>     <num>
1:   Natural Chip        Compny SeaSalt175g        2       6.0
2:                 CCs Nacho Cheese    175g        3       6.3
3:   Smiths Crinkle Cut  Chips Chicken 170g        2       2.9
4:   Smiths Chip Thinly  S/Cream&Onion 175g        5      15.0
5: Kettle Tortilla ChpsHny&Jlpno Chili 150g        3      13.8
6: Old El Paso Salsa   Dip Tomato Mild 300g        1       5.1
> 
> #checking the structure of each column
> str(transactionData)
Classes ‘data.table’ and 'data.frame':  264836 obs. of  8 variables:
 $ DATE          : num  43390 43599 43605 43329 43330 ...
 $ STORE_NBR     : num  1 1 1 2 2 4 4 4 5 7 ...
 $ LYLTY_CARD_NBR: num  1000 1307 1343 2373 2426 ...
 $ TXN_ID        : num  1 348 383 974 1038 ...
 $ PROD_NBR      : num  5 66 61 69 108 57 16 24 42 52 ...
 $ PROD_NAME     : chr  "Natural Chip        Compny SeaSalt175g" "CCs Nacho Cheese    175g" "Smiths Crinkle Cut  Chips Chicken 170g" "Smiths Chip Thinly  S/Cream&Onion 175g" ...
 $ PROD_QTY      : num  2 3 2 5 3 1 1 1 1 2 ...
 $ TOT_SALES     : num  6 6.3 2.9 15 13.8 5.1 5.7 3.6 3.9 7.2 ...
 - attr(*, ".internal.selfref")=<externalptr> 
> 
> #change date format from interger to date format
> transactionData$DATE <- as.Date(transactionData$DATE, origin = "1899-12-30")
> str(transactionData)
Classes ‘data.table’ and 'data.frame':  264836 obs. of  8 variables:
 $ DATE          : Date, format: "2018-10-17" ...
 $ STORE_NBR     : num  1 1 1 2 2 4 4 4 5 7 ...
 $ LYLTY_CARD_NBR: num  1000 1307 1343 2373 2426 ...
 $ TXN_ID        : num  1 348 383 974 1038 ...
 $ PROD_NBR      : num  5 66 61 69 108 57 16 24 42 52 ...
 $ PROD_NAME     : chr  "Natural Chip        Compny SeaSalt175g" "CCs Nacho Cheese    175g" "Smiths Crinkle Cut  Chips Chicken 170g" "Smiths Chip Thinly  S/Cream&Onion 175g" ...
 $ PROD_QTY      : num  2 3 2 5 3 1 1 1 1 2 ...
 $ TOT_SALES     : num  6 6.3 2.9 15 13.8 5.1 5.7 3.6 3.9 7.2 ...
 - attr(*, ".internal.selfref")=<externalptr> 
> 
> #Examine Product Name
> summary(transactionData$PROD_NAME)
   Length     Class      Mode 
   264836 character character 
> unique(transactionData$PROD_NAME)
  [1] "Natural Chip        Compny SeaSalt175g"  
  [2] "CCs Nacho Cheese    175g"                
  [3] "Smiths Crinkle Cut  Chips Chicken 170g"  
  [4] "Smiths Chip Thinly  S/Cream&Onion 175g"  
  [5] "Kettle Tortilla ChpsHny&Jlpno Chili 150g"
  [6] "Old El Paso Salsa   Dip Tomato Mild 300g"
  [7] "Smiths Crinkle Chips Salt & Vinegar 330g"
  [8] "Grain Waves         Sweet Chilli 210g"   
  [9] "Doritos Corn Chip Mexican Jalapeno 150g" 
 [10] "Grain Waves Sour    Cream&Chives 210G"   
 [11] "Kettle Sensations   Siracha Lime 150g"   
 [12] "Twisties Cheese     270g"                
 [13] "WW Crinkle Cut      Chicken 175g"        
 [14] "Thins Chips Light&  Tangy 175g"          
 [15] "CCs Original 175g"                       
 [16] "Burger Rings 220g"                       
 [17] "NCC Sour Cream &    Garden Chives 175g"  
 [18] "Doritos Corn Chip Southern Chicken 150g" 
 [19] "Cheezels Cheese Box 125g"                
 [20] "Smiths Crinkle      Original 330g"       
 [21] "Infzns Crn Crnchers Tangy Gcamole 110g"  
 [22] "Kettle Sea Salt     And Vinegar 175g"    
 [23] "Smiths Chip Thinly  Cut Original 175g"   
 [24] "Kettle Original 175g"                    
 [25] "Red Rock Deli Thai  Chilli&Lime 150g"    
 [26] "Pringles Sthrn FriedChicken 134g"        
 [27] "Pringles Sweet&Spcy BBQ 134g"            
 [28] "Red Rock Deli SR    Salsa & Mzzrlla 150g"
 [29] "Thins Chips         Originl saltd 175g"  
 [30] "Red Rock Deli Sp    Salt & Truffle 150G" 
 [31] "Smiths Thinly       Swt Chli&S/Cream175G"
 [32] "Kettle Chilli 175g"                      
 [33] "Doritos Mexicana    170g"                
 [34] "Smiths Crinkle Cut  French OnionDip 150g"
 [35] "Natural ChipCo      Hony Soy Chckn175g"  
 [36] "Dorito Corn Chp     Supreme 380g"        
 [37] "Twisties Chicken270g"                    
 [38] "Smiths Thinly Cut   Roast Chicken 175g"  
 [39] "Smiths Crinkle Cut  Tomato Salsa 150g"   
 [40] "Kettle Mozzarella   Basil & Pesto 175g"  
 [41] "Infuzions Thai SweetChili PotatoMix 110g"
 [42] "Kettle Sensations   Camembert & Fig 150g"
 [43] "Smith Crinkle Cut   Mac N Cheese 150g"   
 [44] "Kettle Honey Soy    Chicken 175g"        
 [45] "Thins Chips Seasonedchicken 175g"        
 [46] "Smiths Crinkle Cut  Salt & Vinegar 170g" 
 [47] "Infuzions BBQ Rib   Prawn Crackers 110g" 
 [48] "GrnWves Plus Btroot & Chilli Jam 180g"   
 [49] "Tyrrells Crisps     Lightly Salted 165g" 
 [50] "Kettle Sweet Chilli And Sour Cream 175g" 
 [51] "Doritos Salsa       Medium 300g"         
 [52] "Kettle 135g Swt Pot Sea Salt"            
 [53] "Pringles SourCream  Onion 134g"          
 [54] "Doritos Corn Chips  Original 170g"       
 [55] "Twisties Cheese     Burger 250g"         
 [56] "Old El Paso Salsa   Dip Chnky Tom Ht300g"
 [57] "Cobs Popd Swt/Chlli &Sr/Cream Chips 110g"
 [58] "Woolworths Mild     Salsa 300g"          
 [59] "Natural Chip Co     Tmato Hrb&Spce 175g" 
 [60] "Smiths Crinkle Cut  Chips Original 170g" 
 [61] "Cobs Popd Sea Salt  Chips 110g"          
 [62] "Smiths Crinkle Cut  Chips Chs&Onion170g" 
 [63] "French Fries Potato Chips 175g"          
 [64] "Old El Paso Salsa   Dip Tomato Med 300g" 
 [65] "Doritos Corn Chips  Cheese Supreme 170g" 
 [66] "Pringles Original   Crisps 134g"         
 [67] "RRD Chilli&         Coconut 150g"        
 [68] "WW Original Corn    Chips 200g"          
 [69] "Thins Potato Chips  Hot & Spicy 175g"    
 [70] "Cobs Popd Sour Crm  &Chives Chips 110g"  
 [71] "Smiths Crnkle Chip  Orgnl Big Bag 380g"  
 [72] "Doritos Corn Chips  Nacho Cheese 170g"   
 [73] "Kettle Sensations   BBQ&Maple 150g"      
 [74] "WW D/Style Chip     Sea Salt 200g"       
 [75] "Pringles Chicken    Salt Crips 134g"     
 [76] "WW Original Stacked Chips 160g"          
 [77] "Smiths Chip Thinly  CutSalt/Vinegr175g"  
 [78] "Cheezels Cheese 330g"                    
 [79] "Tostitos Lightly    Salted 175g"         
 [80] "Thins Chips Salt &  Vinegar 175g"        
 [81] "Smiths Crinkle Cut  Chips Barbecue 170g" 
 [82] "Cheetos Puffs 165g"                      
 [83] "RRD Sweet Chilli &  Sour Cream 165g"     
 [84] "WW Crinkle Cut      Original 175g"       
 [85] "Tostitos Splash Of  Lime 175g"           
 [86] "Woolworths Medium   Salsa 300g"          
 [87] "Kettle Tortilla ChpsBtroot&Ricotta 150g" 
 [88] "CCs Tasty Cheese    175g"                
 [89] "Woolworths Cheese   Rings 190g"          
 [90] "Tostitos Smoked     Chipotle 175g"       
 [91] "Pringles Barbeque   134g"                
 [92] "WW Supreme Cheese   Corn Chips 200g"     
 [93] "Pringles Mystery    Flavour 134g"        
 [94] "Tyrrells Crisps     Ched & Chives 165g"  
 [95] "Snbts Whlgrn Crisps Cheddr&Mstrd 90g"    
 [96] "Cheetos Chs & Bacon Balls 190g"          
 [97] "Pringles Slt Vingar 134g"                
 [98] "Infuzions SourCream&Herbs Veg Strws 110g"
 [99] "Kettle Tortilla ChpsFeta&Garlic 150g"    
[100] "Infuzions Mango     Chutny Papadums 70g" 
[101] "RRD Steak &         Chimuchurri 150g"    
[102] "RRD Honey Soy       Chicken 165g"        
[103] "Sunbites Whlegrn    Crisps Frch/Onin 90g"
[104] "RRD Salt & Vinegar  165g"                
[105] "Doritos Cheese      Supreme 330g"        
[106] "Smiths Crinkle Cut  Snag&Sauce 150g"     
[107] "WW Sour Cream &OnionStacked Chips 160g"  
[108] "RRD Lime & Pepper   165g"                
[109] "Natural ChipCo Sea  Salt & Vinegr 175g"  
[110] "Red Rock Deli Chikn&Garlic Aioli 150g"   
[111] "RRD SR Slow Rst     Pork Belly 150g"     
[112] "RRD Pc Sea Salt     165g"                
[113] "Smith Crinkle Cut   Bolognese 150g"      
[114] "Doritos Salsa Mild  300g"                
> productWords <- data.table(unlist(strsplit(unique(transactionData$PROD_NAME), " ")))
> setnames(productWords, 'words')
> 
> #Removing words with digits or special character and sorting
> cleaned_words <- productWords[!grepl("[^a-zA-Z]", words)]  
> word_count <- cleaned_words[, .N, by = words][order(-N)] 
> print(word_count)
         words     N
        <char> <int>
  1:             234
  2:     Chips    21
  3:    Smiths    16
  4:   Crinkle    14
  5:       Cut    14
 ---                
165:       Rst     1
166:      Pork     1
167:     Belly     1
168:        Pc     1
169: Bolognese     1
> 
> #summary of the transaction Data
> summary(transactionData)
      DATE              STORE_NBR     LYLTY_CARD_NBR   
 Min.   :2018-07-01   Min.   :  1.0   Min.   :   1000  
 1st Qu.:2018-09-30   1st Qu.: 70.0   1st Qu.:  70021  
 Median :2018-12-30   Median :130.0   Median : 130358  
 Mean   :2018-12-30   Mean   :135.1   Mean   : 135550  
 3rd Qu.:2019-03-31   3rd Qu.:203.0   3rd Qu.: 203094  
 Max.   :2019-06-30   Max.   :272.0   Max.   :2373711  
     TXN_ID           PROD_NBR       PROD_NAME        
 Min.   :      1   Min.   :  1.00   Length:264836     
 1st Qu.:  67602   1st Qu.: 28.00   Class :character  
 Median : 135138   Median : 56.00   Mode  :character  
 Mean   : 135158   Mean   : 56.58                     
 3rd Qu.: 202701   3rd Qu.: 85.00                     
 Max.   :2415841   Max.   :114.00                     
    PROD_QTY         TOT_SALES      
 Min.   :  1.000   Min.   :  1.500  
 1st Qu.:  2.000   1st Qu.:  5.400  
 Median :  2.000   Median :  7.400  
 Mean   :  1.907   Mean   :  7.304  
 3rd Qu.:  2.000   3rd Qu.:  9.200  
 Max.   :200.000   Max.   :650.000  
> 
> #there is no null
> 
> #checking for outliers
> outliers <- transactionData[transactionData$PROD_QTY == 200, ]
> 
> #filtering out customer based on loyalty card number
> library(dplyr)

Attaching package: ‘dplyr’

The following objects are masked from ‘package:data.table’:

    between, first, last

The following objects are masked from ‘package:stats’:

    filter, lag

The following objects are masked from ‘package:base’:

    intersect, setdiff, setequal, union

> transactionData <- transactionData[transactionData$LYLTY_CARD_NBR != "customer_number_to_remove", ]
> 
> #counting the number of transactions by date
> transactionData[, DATE := as.Date(DATE, origin = "1899-12-30")]
> transactions_by_day <- transactionData[, .(Count = .N), by = DATE]
> head(transactions_by_day)
         DATE Count
       <Date> <int>
1: 2018-10-17   732
2: 2019-05-14   758
3: 2019-05-20   755
4: 2018-08-17   711
5: 2018-08-18   737
6: 2019-05-19   779
> summary(transactions_by_day)
      DATE                Count      
 Min.   :2018-07-01   Min.   :648.0  
 1st Qu.:2018-09-29   1st Qu.:706.8  
 Median :2018-12-30   Median :724.0  
 Mean   :2018-12-30   Mean   :727.6  
 3rd Qu.:2019-03-31   3rd Qu.:744.2  
 Max.   :2019-06-30   Max.   :939.0  
> 
> #creating sequence of dates andplotting graph of transaction over time
> transactions_by_day <- transactionData[, .N, by = DATE]
> ggplot(transactions_by_day, aes(x = DATE, y = N)) +
+   geom_line() +
+   labs(x = "Day", y = "Number of transactions", title = "Transactions over time") +
+   scale_x_date(breaks = "1 month") +
+   theme(axis.text.x = element_text(angle = 90, vjust = 0.5))
> 
> #examining days in december
> # Filter for December transactions
> december_transactions <- transactionData[month(DATE) == 12]
> 
> # Calculate number of transactions by day in December
> transactions_by_day_dec <- december_transactions[, .(Count = .N), by = DATE]
> 
> # Plot the transactions for each day in December
> ggplot(transactions_by_day_dec, aes(x = DATE, y = Count)) +
+   geom_line(color = "Green") +
+   labs(title = "Number of Transactions per Day in December",
+        x = "Date",
+        y = "Number of Transactions") +
+   theme_minimal()
> 
> 
> #Branding Features
> 
> #Extract pack size
> transactionData[, PACK_SIZE := parse_number(PROD_NAME)]
> transactionData[, .N, PACK_SIZE][order(PACK_SIZE)]
    PACK_SIZE     N
        <num> <int>
 1:        70  1507
 2:        90  3008
 3:       110 22387
 4:       125  1454
 5:       134 25102
 6:       135  3257
 7:       150 43131
 8:       160  2970
 9:       165 15297
10:       170 19983
11:       175 66390
12:       180  1468
13:       190  2995
14:       200  4473
15:       210  6272
16:       220  1564
17:       250  3169
18:       270  6285
19:       300 15166
20:       330 12540
21:       380  6418
    PACK_SIZE     N
> #The largest size is 380g and the smallest size is 70g
> #Histogramof Pack Size
> ggplot(transactionData, aes(x = PACK_SIZE)) +
+   geom_histogram(binwidth = 1, fill = "blue", color = "black") +  # Adjust binwidth as necessary
+   labs(title = "Distribution of Transactions by Pack Size", 
+        x = "Pack Size", 
+        y = "Number of Transactions") +
+   theme_minimal()
> 
> #creating brand name
> transactionData[, BRAND := word(PROD_NAME, 1)]
> 
> #Check the first few rows to ensure it's created correctly
> head(transactionData[, .(PROD_NAME, BRAND)])
                                  PROD_NAME   BRAND
                                     <char>  <char>
1:   Natural Chip        Compny SeaSalt175g Natural
2:                 CCs Nacho Cheese    175g     CCs
3:   Smiths Crinkle Cut  Chips Chicken 170g  Smiths
4:   Smiths Chip Thinly  S/Cream&Onion 175g  Smiths
5: Kettle Tortilla ChpsHny&Jlpno Chili 150g  Kettle
6: Old El Paso Salsa   Dip Tomato Mild 300g     Old
> 
> # Combine similar brand names
> transactionData[BRAND == "RED", BRAND := "RRD"]  # Standardize RED to RRD
> 
> # Check the unique brand names to ensure they are standardized
> unique_brands <- unique(transactionData$BRAND)
> print(unique_brands)
 [1] "Natural"    "CCs"        "Smiths"     "Kettle"     "Old"       
 [6] "Grain"      "Doritos"    "Twisties"   "WW"         "Thins"     
[11] "Burger"     "NCC"        "Cheezels"   "Infzns"     "Red"       
[16] "Pringles"   "Dorito"     "Infuzions"  "Smith"      "GrnWves"   
[21] "Tyrrells"   "Cobs"       "Woolworths" "French"     "RRD"       
[26] "Tostitos"   "Cheetos"    "Snbts"      "Sunbites"  
> 
> #Examining customer data
> 
> # View the structure of the customer data
> str(customerData)
Classes ‘data.table’ and 'data.frame':  72637 obs. of  3 variables:
 $ LYLTY_CARD_NBR  : int  1000 1002 1003 1004 1005 1007 1009 1010 1011 1012 ...
 $ LIFESTAGE       : chr  "YOUNG SINGLES/COUPLES" "YOUNG SINGLES/COUPLES" "YOUNG FAMILIES" "OLDER SINGLES/COUPLES" ...
 $ PREMIUM_CUSTOMER: chr  "Premium" "Mainstream" "Budget" "Mainstream" ...
 - attr(*, ".internal.selfref")=<externalptr> 
> # Summary statistics for numeric columns
> summary(customerData)
 LYLTY_CARD_NBR     LIFESTAGE         PREMIUM_CUSTOMER  
 Min.   :   1000   Length:72637       Length:72637      
 1st Qu.:  66202   Class :character   Class :character  
 Median : 134040   Mode  :character   Mode  :character  
 Mean   : 136186                                        
 3rd Qu.: 203375                                        
 Max.   :2373711                                        
> # Count the number of customers by loyalty card number (or any other categorical column)
> customerData[, .N, by = LYLTY_CARD_NBR][order(-N)]  # Change LYLTY_CARD_NBR to your categorical column
       LYLTY_CARD_NBR     N
                <int> <int>
    1:           1000     1
    2:           1002     1
    3:           1003     1
    4:           1004     1
    5:           1005     1
   ---                     
72633:        2370651     1
72634:        2370701     1
72635:        2370751     1
72636:        2370961     1
72637:        2373711     1
> 
> #visualization of key concepts
> 
> # Frequency distribution of LIFESTAGE
> customerData[, .N, by = LIFESTAGE][order(-N)]
                LIFESTAGE     N
                   <char> <int>
1:               RETIREES 14805
2:  OLDER SINGLES/COUPLES 14609
3:  YOUNG SINGLES/COUPLES 14441
4:         OLDER FAMILIES  9780
5:         YOUNG FAMILIES  9178
6: MIDAGE SINGLES/COUPLES  7275
7:           NEW FAMILIES  2549
> # Frequency distribution of PREMIUM_CUSTOMER
> customerData[, .N, by = PREMIUM_CUSTOMER][order(-N)]
   PREMIUM_CUSTOMER     N
             <char> <int>
1:       Mainstream 29245
2:           Budget 24470
3:          Premium 18922
> library(ggplot2)
> 
> # Bar plot for LIFESTAGE
> ggplot(customerData, aes(x = LIFESTAGE)) +
+     geom_bar() +
+     labs(title = "Distribution of Customer Lifestages", x = "Lifestage", y = "Number of Customers") +
+     theme(axis.text.x = element_text(angle = 45, hjust = 1))
> 
> # Bar plot for PREMIUM_CUSTOMER
> ggplot(customerData, aes(x = PREMIUM_CUSTOMER)) +
+     geom_bar() +
+     labs(title = "Distribution of Premium Customers", x = "Premium Customer Status", y = "Number of Customers")
> 
> # Check for missing values in each column
> missing_values <- sapply(customerData, function(x) sum(is.na(x)))
> print(missing_values)
  LYLTY_CARD_NBR        LIFESTAGE PREMIUM_CUSTOMER 
               0                0                0 
> #thus there is no missing value
> 
> # Cross-tabulation of LIFESTAGE and PREMIUM_CUSTOMER
> table(customerData$LIFESTAGE, customerData$PREMIUM_CUSTOMER)
                        
                         Budget Mainstream Premium
  MIDAGE SINGLES/COUPLES   1504       3340    2431
  NEW FAMILIES             1112        849     588
  OLDER FAMILIES           4675       2831    2274
  OLDER SINGLES/COUPLES    4929       4930    4750
  RETIREES                 4454       6479    3872
  YOUNG FAMILIES           4017       2728    2433
  YOUNG SINGLES/COUPLES    3779       8088    2574
> 
> #merging transaction data to customer data
> data <- merge(transactionData, customerData, all.x = TRUE)
> 
> # Check for missing customer details
> missing_customers <- data[is.na(LIFESTAGE) | is.na(PREMIUM_CUSTOMER), ]
> 
> # View the first few rows of missing customers
> head(missing_customers)
Key: <LYLTY_CARD_NBR>
Empty data.table (0 rows and 12 cols): LYLTY_CARD_NBR,DATE,STORE_NBR,TXN_ID,PROD_NBR,PROD_NAME...
> 
> # Summary of missing values for each relevant column
> missing_summary <- data.table(
+ +     LIFESTAGE_Missing = sum(is.na(data$LIFESTAGE)),
Error: unexpected '=' in:
"missing_summary <- data.table(
+     LIFESTAGE_Missing ="
> +     PREMIUM_CUSTOMER_Missing = sum(is.na(data$PREMIUM_CUSTOMER)),
Error: unexpected ',' in "+     PREMIUM_CUSTOMER_Missing = sum(is.na(data$PREMIUM_CUSTOMER)),"
> +     CUSTOMER_ID_Missing = sum(is.na(data$CUSTOMER_ID))  # Add other relevant columns as necessary
Error: object 'CUSTOMER_ID_Missing' not found
> + )
Error: unexpected ')' in "+ )"
> 
> 
> # Save the merged dataset as a CSV file
> fwrite(data, paste0("../desktop/QVI_data.csv"))
> 
> #DATA ANALYSIS
> 
> #Calculating total sales by LIFESTAGE and PREMIUM_CUSTOMER
> 
> # Calculate total sales by LIFESTAGE and PREMIUM_CUSTOMER
> total_sales_by_segment <- data[, .(Total_Sales = sum(TOT_SALES, na.rm = TRUE)), 
+                                  by = .(LIFESTAGE, PREMIUM_CUSTOMER)]
> 
> # Create a bar plot
> ggplot(total_sales_by_segment, aes(x = LIFESTAGE, y = Total_Sales, fill = PREMIUM_CUSTOMER)) +
+   geom_bar(stat = "identity", position = "dodge") +  # Use "stack" for stacked bars
+   labs(title = "Total Chip Sales by Customer Segment", 
+        x = "Lifestage", 
+        y = "Total Sales") +
+   theme_minimal() +
+   scale_fill_brewer(palette = "Set1")
> 
> # View the summary table
> print(total_sales_by_segment)
                 LIFESTAGE PREMIUM_CUSTOMER Total_Sales
                    <char>           <char>       <num>
 1:  YOUNG SINGLES/COUPLES          Premium    41642.10
 2:  YOUNG SINGLES/COUPLES       Mainstream   157621.60
 3:         YOUNG FAMILIES           Budget   139345.85
 4:  OLDER SINGLES/COUPLES       Mainstream   133393.80
 5: MIDAGE SINGLES/COUPLES       Mainstream    90803.85
 6:  YOUNG SINGLES/COUPLES           Budget    61141.60
 7:           NEW FAMILIES          Premium    11491.10
 8:         OLDER FAMILIES       Mainstream   103445.55
 9:               RETIREES           Budget   113147.80
10:  OLDER SINGLES/COUPLES          Premium   132263.15
11:         OLDER FAMILIES           Budget   168363.25
12: MIDAGE SINGLES/COUPLES          Premium    58432.65
13:         OLDER FAMILIES          Premium    81958.40
14:               RETIREES       Mainstream   155677.05
15:               RETIREES          Premium    97646.05
16:         YOUNG FAMILIES       Mainstream    92788.75
17: MIDAGE SINGLES/COUPLES           Budget    35514.80
18:           NEW FAMILIES       Mainstream    17013.90
19:  OLDER SINGLES/COUPLES           Budget   136769.80
20:         YOUNG FAMILIES          Premium    84025.50
21:           NEW FAMILIES           Budget    21928.45
                 LIFESTAGE PREMIUM_CUSTOMER Total_Sales
> 
> ##Summaryof Unique Customers
> #Calculate the number of unique customers
> unique_customers <- data[, .(Unique_Customers = uniqueN(LYLTY_CARD_NBR)), 
+                           by = .(LIFESTAGE, PREMIUM_CUSTOMER)]
> 
> # View the unique customers summary
> print(unique_customers)
                 LIFESTAGE PREMIUM_CUSTOMER Unique_Customers
                    <char>           <char>            <int>
 1:  YOUNG SINGLES/COUPLES          Premium             2574
 2:  YOUNG SINGLES/COUPLES       Mainstream             8088
 3:         YOUNG FAMILIES           Budget             4017
 4:  OLDER SINGLES/COUPLES       Mainstream             4930
 5: MIDAGE SINGLES/COUPLES       Mainstream             3340
 6:  YOUNG SINGLES/COUPLES           Budget             3779
 7:           NEW FAMILIES          Premium              588
 8:         OLDER FAMILIES       Mainstream             2831
 9:               RETIREES           Budget             4454
10:  OLDER SINGLES/COUPLES          Premium             4750
11:         OLDER FAMILIES           Budget             4675
12: MIDAGE SINGLES/COUPLES          Premium             2431
13:         OLDER FAMILIES          Premium             2274
14:               RETIREES       Mainstream             6479
15:               RETIREES          Premium             3872
16:         YOUNG FAMILIES       Mainstream             2728
17: MIDAGE SINGLES/COUPLES           Budget             1504
18:           NEW FAMILIES       Mainstream              849
19:  OLDER SINGLES/COUPLES           Budget             4929
20:         YOUNG FAMILIES          Premium             2433
21:           NEW FAMILIES           Budget             1112
                 LIFESTAGE PREMIUM_CUSTOMER Unique_Customers
> 
> #Create a plot for unique customers
> ggplot(unique_customers, aes(x = LIFESTAGE, y = Unique_Customers, fill = PREMIUM_CUSTOMER)) +
+   geom_bar(stat = "identity", position = "dodge") + 
+   labs(title = "Number of Unique Customers by Lifestyle", 
+        x = "Lifestage", 
+        y = "Unique Customers") +
+   theme_minimal() +
+   scale_fill_brewer(palette = "Set1") 
> 
> #Average number of units per customerby LIFESTAGE and PREMIUM_CUSTOMER
> #Calculate total units purchased per customer
> total_units_per_customer <- data[, .(Total_Units = sum(PROD_QTY)), by = LYLTY_CARD_NBR]
> 
> #Merge the total units data with customer segments
> total_units_per_customer <- merge(total_units_per_customer, customerData, by = "LYLTY_CARD_NBR", all.x = TRUE)
> 
> #Calculate average units per customer by LIFESTAGE and PREMIUM_CUSTOMER
> avg_units_per_customer <- total_units_per_customer[, .(Average_Units = mean(Total_Units, na.rm = TRUE)), 
+                                                     by = .(LIFESTAGE, PREMIUM_CUSTOMER)]
> 
> #View the average units summary
> print(avg_units_per_customer)
                 LIFESTAGE PREMIUM_CUSTOMER Average_Units
                    <char>           <char>         <num>
 1:  YOUNG SINGLES/COUPLES          Premium      4.402098
 2:  YOUNG SINGLES/COUPLES       Mainstream      4.776459
 3:         YOUNG FAMILIES           Budget      9.238486
 4:  OLDER SINGLES/COUPLES       Mainstream      7.098783
 5: MIDAGE SINGLES/COUPLES       Mainstream      6.796108
 6:  YOUNG SINGLES/COUPLES           Budget      4.411485
 7:           NEW FAMILIES          Premium      5.028912
 8:         OLDER FAMILIES       Mainstream      9.804309
 9:               RETIREES           Budget      6.458015
10:  OLDER SINGLES/COUPLES          Premium      7.154947
11:         OLDER FAMILIES           Budget      9.639572
12: MIDAGE SINGLES/COUPLES          Premium      6.386672
13:         OLDER FAMILIES          Premium      9.749780
14:               RETIREES       Mainstream      6.253743
15:               RETIREES          Premium      6.426653
16:         YOUNG FAMILIES       Mainstream      9.180352
17: MIDAGE SINGLES/COUPLES           Budget      6.313830
18:           NEW FAMILIES       Mainstream      5.087161
19:  OLDER SINGLES/COUPLES           Budget      7.145466
20:         YOUNG FAMILIES          Premium      9.209207
21:           NEW FAMILIES           Budget      5.009892
                 LIFESTAGE PREMIUM_CUSTOMER Average_Units
> 
> #Create a plot for average units per customer
> ggplot(avg_units_per_customer, aes(x = LIFESTAGE, y = Average_Units, fill = PREMIUM_CUSTOMER)) +
+   geom_bar(stat = "identity", position = "dodge") +  # Use "stack" for stacked bars if needed
+   labs(title = "Average Number of Units per Customer by Segment", 
+        x = "Lifestage", 
+        y = "Average Units per Customer") +
+   theme_minimal() +
+   scale_fill_brewer(palette = "Set1")
> 
> #Calculating Price Per Unit Sold
> #Calculate total sales and total units purchased per customer
> total_sales_per_customer <- data[, .(Total_Sales = sum(TOT_SALES)), by = LYLTY_CARD_NBR]
> total_units_per_customer <- data[, .(Total_Units = sum(PROD_QTY)), by = LYLTY_CARD_NBR]
> 
> #Merge the sales and units data
> customer_summary <- merge(total_sales_per_customer, total_units_per_customer, by = "LYLTY_CARD_NBR", all.x = TRUE)
> 
> #Calculate average price per unit
> customer_summary[, Avg_Price_Per_Unit := Total_Sales / Total_Units]
> 
> #Merge with customer data to get segments
> customer_summary <- merge(customer_summary, customerData, by = "LYLTY_CARD_NBR", all.x = TRUE)
> 
> #Calculate average price per unit by LIFESTAGE and PREMIUM_CUSTOMER
> avg_price_per_unit <- customer_summary[, .(Average_Price = mean(Avg_Price_Per_Unit, na.rm = TRUE)), 
+                                         by = .(LIFESTAGE, PREMIUM_CUSTOMER)]
> 
> # View the average price summary
> print(avg_price_per_unit)
                 LIFESTAGE PREMIUM_CUSTOMER Average_Price
                    <char>           <char>         <num>
 1:  YOUNG SINGLES/COUPLES          Premium      3.587880
 2:  YOUNG SINGLES/COUPLES       Mainstream      4.144507
 3:         YOUNG FAMILIES           Budget      3.836333
 4:  OLDER SINGLES/COUPLES       Mainstream      3.811121
 5: MIDAGE SINGLES/COUPLES       Mainstream      4.077885
 6:  YOUNG SINGLES/COUPLES           Budget      3.593321
 7:           NEW FAMILIES          Premium      3.865264
 8:         OLDER FAMILIES       Mainstream      3.810482
 9:               RETIREES           Budget      3.960887
10:  OLDER SINGLES/COUPLES          Premium      3.936156
11:         OLDER FAMILIES           Budget      3.812175
12: MIDAGE SINGLES/COUPLES          Premium      3.763974
13:         OLDER FAMILIES          Premium      3.779584
14:               RETIREES       Mainstream      3.835157
15:               RETIREES          Premium      3.964451
16:         YOUNG FAMILIES       Mainstream      3.796891
17: MIDAGE SINGLES/COUPLES           Budget      3.730363
18:           NEW FAMILIES       Mainstream      3.902435
19:  OLDER SINGLES/COUPLES           Budget      3.901387
20:         YOUNG FAMILIES          Premium      3.823272
21:           NEW FAMILIES           Budget      3.894587
                 LIFESTAGE PREMIUM_CUSTOMER Average_Price
> 
> #Create a plot for average price per unit
> ggplot(avg_price_per_unit, aes(x = LIFESTAGE, y = Average_Price, fill = PREMIUM_CUSTOMER)) +
+   geom_bar(stat = "identity", position = "dodge") +  # Use "stack" for stacked bars if needed
+   labs(title = "Average Price per Unit by Segment", 
+        x = "Lifestage", 
+        y = "Average Price per Unit") +
+   theme_minimal() +
+   scale_fill_brewer(palette = "Set1")
> 
> #Independent t-test between mainstream vs premium and budget midage and young singles and couples
> library(dplyr)
> 
> #Subset the data for Mainstream vs. Premium
> mainstream_customers <- customer_summary %>%
+   filter(PREMIUM_CUSTOMER == "Mainstream")
> 
> premium_customers <- customer_summary %>%
+   filter(PREMIUM_CUSTOMER == "Premium")
> 
> #Combine into one data frame for t-test
> mainstream_vs_premium <- rbind(
+   data.frame(Avg_Price = mainstream_customers$Avg_Price_Per_Unit, Group = "Mainstream"),
+   data.frame(Avg_Price = premium_customers$Avg_Price_Per_Unit, Group = "Premium")
+ )
> 
> #Perform t-test for Mainstream vs Premium
> t_test_mainstream_premium <- t.test(Avg_Price ~ Group, data = mainstream_vs_premium)
> 
> # Print the results of the t-test
> print(t_test_mainstream_premium)

        Welch Two Sample t-test

data:  Avg_Price by Group
t = 13.386, df = 39368, p-value < 2.2e-16
alternative hypothesis: true difference in means between group Mainstream and group Premium is not equal to 0
95 percent confidence interval:
 0.08831249 0.11860982
sample estimates:
mean in group Mainstream    mean in group Premium 
                3.940375                 3.836914 

> #Subset the data for Budget Midage vs Young Singles and Couples
> budget_midage_customers <- customer_summary %>%
+   filter(LIFESTAGE == "Budget - Midage")
> 
> young_singles_couples <- customer_summary %>%
+   filter(LIFESTAGE == "Mainstream - young singles/couples")
> 
> #Combine into one data frame for t-test
> budget_midage_vs_young <- rbind(
+   data.frame(Avg_Price = budget_midage_customers$Avg_Price_Per_Unit, Group = "Budget Midage"),
+   data.frame(Avg_Price = young_singles_couples$Avg_Price_Per_Unit, Group = "Young Singles/Couples")
+ )
Error in data.frame(Avg_Price = budget_midage_customers$Avg_Price_Per_Unit,  : 
  arguments imply differing number of rows: 0, 1
> 
> #Perform t-test for Budget Midage vs Young Singles/Couples
> t_test_budget_young <- t.test(Avg_Price ~ Group, data = budget_midage_vs_young)
> 
> #Print the results of the t-test
> print(t_test_budget_young)

        Welch Two Sample t-test

data:  Avg_Price by Group
t = 0.0072549, df = 15738, p-value = 0.9942
alternative hypothesis: true difference in means between group Budget Midage and group Young Singles/Couples is not equal to 0
95 percent confidence interval:
 -0.02395022  0.02412817
sample estimates:
        mean in group Budget Midage mean in group Young Singles/Couples 
                           3.901144                            3.901055 

> #The t-test results in a p-value of < 2.2e-16, indicating that the unit price for Mainstream, young, and mid-age singles and couples is significantly higher than that of Budget or Premium, young and mid-age singles and couples. This significant p-value confirms that the observed difference in unit price between these groups is statistically meaningful.
> 
> 
> ##Check for brand reference
> 
> #Filter data for the target segment - Mainstream young singles/couples
> mainstream_young <- data %>%
+   filter(LIFESTAGE == "YOUNG SINGLES/COUPLES" & PREMIUM_CUSTOMER == "Mainstream")
> 
> #Calculate the total number of purchases by brand within this segment
> brand_counts <- mainstream_young %>%
+   group_by(BRAND) %>%
+   summarise(Segment_Purchases = n()) %>%
+   arrange(desc(Segment_Purchases))
> 
> #Calculate total purchases for each brand across all segments
> total_brand_counts <- data %>%
+   group_by(BRAND) %>%
+   summarise(Total_Purchases = n())
> 
> #Join the data to get purchase proportions for this segment vs. overall
> brand_preference <- brand_counts %>%
+   left_join(total_brand_counts, by = "BRAND") %>%
+   mutate(Segment_Preference = Segment_Purchases / Total_Purchases) %>%
+   arrange(desc(Segment_Preference))
> 
> #Visualize the top brands by preference for this segment
> ggplot(brand_preference, aes(x = reorder(BRAND, -Segment_Preference), y = Segment_Preference)) +
+   geom_bar(stat = "identity", fill = "skyblue") +
+   labs(title = "Brand Preference for Mainstream Young Singles/Couples",
+        x = "Brand",
+        y = "Preference Ratio") +
+   theme_minimal() +
+   theme(axis.text.x = element_text(angle = 45, hjust = 1))
> 
> #We can see that Tyrells is the most prefered brand and burger is the least prefered.
> 
> #Comparing mainstream - young singles/couples to the rest of the ppulation
> #Filter data for the target segment - Mainstream young singles/couples
> mainstream_young <- data %>%
+   filter(LIFESTAGE == "YOUNG SINGLES/COUPLES" & PREMIUM_CUSTOMER == "Mainstream")
> 
> #Calculate the pack size distribution for the target segment
> pack_size_counts_target <- mainstream_young %>%
+   group_by(PACK_SIZE) %>%
+   summarise(Target_Segment_Count = n()) %>%
+   arrange(desc(Target_Segment_Count))
> 
> #Calculate the pack size distribution for the rest of the population
> pack_size_counts_rest <- data %>%
+   filter(!(LIFESTAGE == "YOUNG SINGLES/COUPLES" & PREMIUM_CUSTOMER == "Mainstream")) %>%
+   group_by(PACK_SIZE) %>%
+   summarise(Rest_Population_Count = n())
> 
> #Merge and calculate the preference for pack sizes within the target segment vs. the rest
> pack_size_preference <- pack_size_counts_target %>%
+   left_join(pack_size_counts_rest, by = "PACK_SIZE") %>%
+   mutate(Target_Preference_Ratio = Target_Segment_Count / sum(Target_Segment_Count),
+          Rest_Population_Ratio = Rest_Population_Count / sum(Rest_Population_Count)) %>%
+   arrange(desc(Target_Preference_Ratio))
> 
> #Visualize the pack size preference comparison
> ggplot(pack_size_preference, aes(x = reorder(PACK_SIZE, -Target_Preference_Ratio))) +
+   geom_bar(aes(y = Target_Preference_Ratio, fill = "Target Segment"), stat = "identity", position = "dodge") +
+   geom_bar(aes(y = Rest_Population_Ratio, fill = "Rest of Population"), stat = "identity", position = "dodge") +
+   labs(title = "Preferred Pack Size Comparison - Mainstream Young Singles/Couples vs. Rest of Population",
+        x = "Pack Size",
+        y = "Preference Ratio") +
+   scale_fill_manual(values = c("skyblue", "coral")) +
+   theme_minimal() +
+   theme(axis.text.x = element_text(angle = 45, hjust = 1))
> 
> ##Yes, Our target segment (Mainstream - young singles/couples) tends to buy larger pack of chips compared to the rest of the population and also more pack size
> save.image("C:\\Users\\USER\\Desktop\\R Tutorial\\Retail and Strategy Analytics")
> save.image("C:\\Users\\USER\\Desktop\\let see")
> 
