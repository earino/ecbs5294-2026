---
title: The data
layout: default
nav_order: 8
---

# The data

Every file in this course is **real, public data**: no synthetic rows, no edited values. Each lab and homework carries
its files in `data/raw/`, committed on purpose and never edited, with a `DATA.md` beside them giving the exact source,
the date it was fetched, its license and attribution, and what was changed for the course (a subset, a format). The
repository's `LICENSE.md` lists each dataset's license. This page is the catalogue.

What a file *does* — which rows are not what they claim to be, what "missing" looks like in it — is not written here.
Finding it is the work, and the census is how.

## Online Retail II

| | |
|---|---|
| **What it is** | Every invoice line of a UK-based online retailer of gifts, 1 December 2009 to 9 December 2010. |
| **Source** | Daqing Chen, *Online Retail II*, UCI Machine Learning Repository, [doi:10.24432/C5CG6D](https://doi.org/10.24432/C5CG6D) (the 2009–2010 sheet). |
| **License** | CC BY 4.0. |
| **As shipped** | `online_retail.parquet`: all 525,461 lines of that sheet, 8 columns, 3.2 MB. Converted from CSV to Parquet with the types DuckDB inferred; no rows or values changed. |
| **Used in** | the setup check; the Block 1 lecture (in the Lab 1 project); the Block 2 lecture's "why SQL" demonstration; Lab 2; Homework 1. |

## Our World in Data — CO₂ and greenhouse-gas emissions

| | |
|---|---|
| **What it is** | Emissions, population, and GDP by country and year, as Our World in Data publishes them. |
| **Source** | Our World in Data, [*CO₂ and Greenhouse Gas Emissions*](https://github.com/owid/co2-data) (Rosado, Ritchie, Roser, Mathieu, Macdonald). |
| **License** | CC BY 4.0 for Our World in Data's own work; each underlying series keeps its source's terms, named per column in the codebook. Cite Our World in Data and the named source. |
| **As shipped** | `owid_co2.csv`: 15 of the file's columns, years 1990 to 2024, 8,883 rows; `owid_codebook.csv`, what each of those columns means. `countries.csv` (7,665 rows) is a further subset, made with the filter Lab 1 builds, for Block 2. |
| **Used in** | Lab 1; the Block 2 lecture (`countries.csv`, in the Lab 2 project); Homework 3. |

## Brazilian E-Commerce Public Dataset by Olist

| | |
|---|---|
| **What it is** | Orders from a Brazilian online marketplace, 2016 to 2018, in eight linked tables: orders, their items, payments, and reviews; customers, products, sellers; and a translation of the product categories. |
| **Source** | Olist, [*Brazilian E-Commerce Public Dataset by Olist*](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), Kaggle. |
| **License** | **CC BY-NC-SA 4.0.** NonCommercial: redistributed here for teaching only. ShareAlike: the subset is an adaptation, shared under the same license. The course's own code and text are not relicensed by it. |
| **As shipped** | A **selected** subset, not a representative one: 2,653 of the 99,441 orders, chosen so that the situations the labs are about are present, with every item, payment, and review of those orders and every customer, product, and seller they refer to. The selection rule is in `DATA.md`. `orders` 2,653 rows · `order_items` 3,024 · `order_payments` 2,790 · `order_reviews` 2,681 · `customers` 2,653 · `products` 2,217 · `sellers` 903 · `product_category_name_translation` 71. |
| **Used in** | the Block 3 lecture and Lab 3; Lab 4 (orders and payments); Lab 6; Homework 2; Knowledge check 3 and its ungraded retry, which quote small, unchanged extracts. |

## Eurostat — municipal waste (`env_wasmun`)

| | |
|---|---|
| **What it is** | Municipal waste by waste-management operation, country, and year, in Eurostat's own file layout. |
| **Source** | Eurostat, dataset `env_wasmun`, through Eurostat's dissemination API (TSV). |
| **License** | CC BY 4.0. Attribution: *Source: Eurostat, env_wasmun*, © European Union. |
| **As shipped** | `env_wasmun.tsv`, unmodified: 742 rows, one column per year from 2010 to 2024. The first column packs four codes into one; Eurostat's code lists for them and for its observation flags ship beside it (`codelist_*.tsv`). |
| **Used in** | Lab 5. |

## Eurostat — greenhouse-gas emissions by source sector (`env_air_gge`)

| | |
|---|---|
| **What it is** | Greenhouse-gas emissions by country, source sector, and year, in Eurostat's own file layout. |
| **Source** | Eurostat, dataset `env_air_gge`, through Eurostat's dissemination API (TSV). |
| **License** | CC BY 4.0. Attribution: *Source: Eurostat, env_air_gge*, © European Union. |
| **As shipped** | `env_air_gge_ghg.tsv`: the rows for all greenhouse gases together in CO₂ equivalent (`GHG`), every source sector, every country, every unit the file carries; 10,444 rows, one column per year from 2010 to 2024. The header and every kept row are byte for byte as published. |
| **Used in** | the Block 5 lecture (in the Lab 5 project); Homework 3. |

## Frankfurter — European Central Bank reference rates

| | |
|---|---|
| **What it is** | The ECB's daily euro reference rates for the Brazilian real and the US dollar, 1 September 2016 to 31 October 2018, as one API response. |
| **Source** | The [Frankfurter API](https://frankfurter.dev), which serves the European Central Bank's euro foreign exchange reference rates. The request is written in `DATA.md`. |
| **License** | ECB statistics may be reused with the source acknowledged: *Source: European Central Bank*. The Frankfurter API is open source (MIT). |
| **As shipped** | `api/frankfurter_eur.json`: the response exactly as received — one JSON document with rates for 555 dates. The ECB publishes on its business days only. |
| **Used in** | Lab 4; Homework 2. |

## World Bank — World Development Indicators

| | |
|---|---|
| **What it is** | Population (`SP.POP.TOTL`) and GDP at purchasing-power parity in constant international dollars (`NY.GDP.MKTP.PP.KD`), all countries and groups of countries, 2010 to 2024; and the World Bank's list of countries and groups. |
| **Source** | World Bank Indicators API v2, [api.worldbank.org](https://api.worldbank.org/v2). The requests are written in `DATA.md`. |
| **License** | CC BY 4.0. Attribution: *World Bank, World Development Indicators*. |
| **As shipped** | Each API response exactly as received, one file per page, under `api/worldbank/`: four pages per indicator (3,975 records each), and one page of country metadata (295 entries). |
| **Used in** | the Block 4 lecture (population and the country list, in the Lab 4 project); Homework 3 (both indicators). |

## EU-27 country codes

| | |
|---|---|
| **What it is** | The 27 EU member states (as of 2020), one row each, with the code each source uses for them: Eurostat's, the ISO three-letter code Our World in Data uses, and the World Bank's. |
| **Source** | Made for this course, by hand, from the three sources' own documentation. |
| **License** | CC BY 4.0, as the course materials. |
| **As shipped** | `country_codes.csv`, 27 rows. |
| **Used in** | Homework 3. |

## Using the data outside the course

Follow each dataset's license. For Olist that means non-commercial use only, and the same license on anything you
share that is built from it. For the others, attribution as written above. Your homework zip contains `data/raw/`,
`DATA.md`, and `LICENSE.md`: it is for grading only.
