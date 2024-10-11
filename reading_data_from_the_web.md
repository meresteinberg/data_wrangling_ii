Reading Data from the Web
================
2024-10-11

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"
drug_use_html = read_html(url)

drug_use_html
```

    ## {html_document}
    ## <html lang="en">
    ## [1] <head>\n<link rel="P3Pv1" href="http://www.samhsa.gov/w3c/p3p.xml">\n<tit ...
    ## [2] <body>\r\n\r\n<noscript>\r\n<p>Your browser's Javascript is off. Hyperlin ...

Get the pieces I actually need –adding first() gives us just the first
table

``` r
marj_use_df=
drug_use_html |> 
  html_table() |> 
  first() |> 
  slice(-1)
```

Learning Assessment –header=TRUE says first row is the header

``` r
url= "https://www.bestplaces.net/cost_of_living/city/new_york/new_york"

cost_html=read_html(url)

cost_html |> 
  html_table(header= TRUE) |> 
  first()
```

    ## # A tibble: 8 × 4
    ##   Categories       `New York` `New York` `United States`
    ##   <chr>            <chr>      <chr>      <chr>          
    ## 1 Overall          172.5      121.5      100.0          
    ## 2 Grocery          116.6      103.8      100            
    ## 3 Health           127.6      120.7      100.0          
    ## 4 Housing          224.3      127.9      100.0          
    ## 5 Median Home Cost $677,200   $413,600   $338,100       
    ## 6 Utilities        150.5%     115.9%     100.0%         
    ## 7 Transportation   181.1      140.7      100.0          
    ## 8 Miscellaneous    136.4      121.8      100.0

OR

``` r
nyc_cost = 
  read_html("https://www.bestplaces.net/cost_of_living/city/new_york/new_york") |>
  html_table(header = TRUE) |>
  first()
```

## CSS Selectors

``` r
swm_html = 
  read_html("https://www.imdb.com/list/ls070150896/") 

title_vec=
  swm_html |> 
  html_elements(".ipc-title-link-wrapper .ipc-title__text") |> 
  html_text()

runtime_vec=  
  swm_html |> 
  html_elements(".dli-title-metadata-item:nth-child(2)") |> 
  html_text()

swm_df=
  tibble(
    title= title_vec,
    runtime= runtime_vec
  )
```

Steps: click selector gatchet bookmark, click what you want and can then
click on what you dont want, and can then copy the CSS tag at the bottom
that is pasted into html_elements()

Lets import some books

``` r
books_html=read_html("https://books.toscrape.com/")

books_html |> 
  html_elements(".product_pod a") |> 
  html_text()
```

    ##  [1] ""                                     
    ##  [2] "A Light in the ..."                   
    ##  [3] ""                                     
    ##  [4] "Tipping the Velvet"                   
    ##  [5] ""                                     
    ##  [6] "Soumission"                           
    ##  [7] ""                                     
    ##  [8] "Sharp Objects"                        
    ##  [9] ""                                     
    ## [10] "Sapiens: A Brief History ..."         
    ## [11] ""                                     
    ## [12] "The Requiem Red"                      
    ## [13] ""                                     
    ## [14] "The Dirty Little Secrets ..."         
    ## [15] ""                                     
    ## [16] "The Coming Woman: A ..."              
    ## [17] ""                                     
    ## [18] "The Boys in the ..."                  
    ## [19] ""                                     
    ## [20] "The Black Maria"                      
    ## [21] ""                                     
    ## [22] "Starving Hearts (Triangular Trade ..."
    ## [23] ""                                     
    ## [24] "Shakespeare's Sonnets"                
    ## [25] ""                                     
    ## [26] "Set Me Free"                          
    ## [27] ""                                     
    ## [28] "Scott Pilgrim's Precious Little ..."  
    ## [29] ""                                     
    ## [30] "Rip it Up and ..."                    
    ## [31] ""                                     
    ## [32] "Our Band Could Be ..."                
    ## [33] ""                                     
    ## [34] "Olio"                                 
    ## [35] ""                                     
    ## [36] "Mesaerion: The Best Science ..."      
    ## [37] ""                                     
    ## [38] "Libertarianism for Beginners"         
    ## [39] ""                                     
    ## [40] "It's Only the Himalayas"

## Using an API

Get water data.

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") |> 
  content("parsed")
```

    ## Rows: 45 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Get BRFSS data–limit parameter is based on instructions from this
website

``` r
brfss_smart2010 = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query = list("$limit" = 5000)) |> 
  content("parsed")
```

    ## Rows: 5000 Columns: 23
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (16): locationabbr, locationdesc, class, topic, question, response, data...
    ## dbl  (6): year, sample_size, data_value, confidence_limit_low, confidence_li...
    ## lgl  (1): locationid
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Pokemon API

``` r
poke = 
  GET("http://pokeapi.co/api/v2/pokemon/ditto") |>
  content()

poke[["name"]]
```

    ## [1] "ditto"
