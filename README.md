
# displaced

[![CRAN
status](https://www.r-pkg.org/badges/version/displaced)](https://cran.r-project.org/package=displaced)

`displaced` is an R package designed to facilitate access to the United
Nations High Commissioner for Refugees (UNHCR) Refugee Data Finder. It
provides an easy-to-use interface to the database, which covers forcibly
displaced populations, including refugees, asylum-seekers, internally
displaced people, stateless people, and others over a span of more than
70 years.

This package provides data from three major sources:

1.  Data from UNHCR’s annual statistical activities dating back to 1951.
2.  Data from the United Nations Relief and Works Agency for Palestine
    Refugees in the Near East (UNRWA), specifically for registered
    Palestine refugees under UNRWA’s mandate.
3.  Data from the Internal Displacement Monitoring Centre (IDMC) on
    people displaced within their country due to conflict or violence.

## Datasets

The `displaced` package includes eight datasets:

1.  `population`: Data on forcibly displaced and stateless persons by
    year, including refugees, asylum-seekers, returned refugees,
    internally displaced persons (IDPs), and stateless persons.

2.  `idmc`: Data from the Internal Displacement Monitoring Centre on the
    total number of IDPs due to conflict and violence.

3.  `asylum_applications`: Data on asylum applications including
    procedure type and application type.

4.  `asylum_decisions`: Data on asylum decisions, including recognised,
    rejected, and otherwise closed claims.

5.  `demographics`: Demographic and sub-national data, where available,
    including age and sex disaggregation.

6.  `solutions`: Data on durable solutions for refugees and IDPs.

7.  `unrwa`: Data on registered Palestine refugees under UNRWA’s
    mandate.

8.  `countries`: Country codes, names, UN major areas, and UNHCR
    regional bureaux/operations.

Please check the individual dataset description for more details on the
fields each dataset provides.

## Installation

Install either from CRAN with:

``` r
install.packages("displaced")
```

Or retrieve the development version from Github using the
[`pak`](https://pak.r-lib.org/) package:

``` r
pak::pkg_install("PopulationStatistics/displaced")
```

## Usage

Here are some examples of how you can use the `displaced` package. The
`population` dataset can be used to easily get data on forced
displacement or statelessness.

``` r
library(displaced)
glimpse(displaced::population)
#> Rows: 120,338
#> Columns: 16
#> $ year              <dbl> 1951, 1951, 1951, 1951, 1951, 1951, 1951, 1951, 1951, 1951, 1951, 1951…
#> $ coo_name          <chr> "Unknown", "Unknown", "Unknown", "Unknown", "Unknown", "Unknown", "Unk…
#> $ coo               <chr> "UKN", "UKN", "UKN", "UKN", "UKN", "UKN", "UKN", "UKN", "UKN", "UKN", …
#> $ coo_iso           <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
#> $ coa_name          <chr> "Australia", "Austria", "Belgium", "Canada", "Denmark", "France", "Uni…
#> $ coa               <chr> "AUL", "AUS", "BEL", "CAN", "DEN", "FRA", "GBR", "GFR", "GRE", "HKG", …
#> $ coa_iso           <chr> "AUS", "AUT", "BEL", "CAN", "DNK", "FRA", "GBR", "DEU", "GRC", "HKG", …
#> $ refugees          <dbl> 180000, 282000, 55000, 168511, 2000, 290000, 208000, 265000, 18000, 30…
#> $ asylum_seekers    <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0…
#> $ returned_refugees <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0…
#> $ idps              <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0…
#> $ returned_idps     <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0…
#> $ stateless         <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0…
#> $ ooc               <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0…
#> $ oip               <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
#> $ hst               <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
```

The `population` dataset can be used to get the 10 countries of origin
with highest number of refugees, people in refugee-like situations
including people in need of internation protection in 2022.

``` r
library(dplyr)
ref_coo_10 <- displaced::population |>
  filter(year == 2022) |>
  summarise(refugees = sum(refugees, na.rm = TRUE) + sum(oip, na.rm = TRUE),
            .by = coo_name) |>
  slice_max(order_by = refugees, n = 10)
ref_coo_10
#> # A tibble: 10 × 2
#>    coo_name                           refugees
#>    <chr>                                 <dbl>
#>  1 Syrian Arab Rep.                    6547818
#>  2 Ukraine                             5679880
#>  3 Afghanistan                         5661675
#>  4 Venezuela (Bolivarian Republic of)  5451832
#>  5 South Sudan                         2294983
#>  6 Myanmar                             1253111
#>  7 Dem. Rep. of the Congo               931903
#>  8 Sudan                                836756
#>  9 Somalia                              790513
#> 10 Central African Rep.                 748327
```

We can use `ggplot2` and the
[`unhcrthemes`](https://github.com/vidonne/unhcrthemes) to visualize our
data while following the [UNHCR data visualization
guidelines](https://dataviz.unhcr.org/general_guidance/).

``` r
library(ggplot2)
library(unhcrthemes)

lbl <- scales::label_number()

ref_coo_10 |>
  ggplot(aes(refugees, reorder(coo_name, refugees))) +
  geom_col(fill = unhcr_pal(n = 1, "pal_blue"),
           width = 0.8) +
  geom_text(aes(label = lbl(refugees)),
            hjust = -0.2) +
  scale_x_continuous(expand = expansion(mult = c(0, 0.1))) +
  theme_unhcr(font_size = 12,
              grid = FALSE,
              axis = FALSE,
              axis_title = FALSE,
              axis_text = "y")
```

<img src="man/figures/README-unnamed-chunk-6-1.png" width="100%" />

## License

This package is released under the [Creative Commons Attribution for
Intergovernmental
Organisations](https://creativecommons.org/licenses/by/3.0/igo/)
