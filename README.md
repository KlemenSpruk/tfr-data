# FAA TFR Data
The code in this project compiles a dataset of temporary flight restrictions, and their corresponding shapefiles, issued by the FAA.

The FAA lists its active TFRs [online](http://tfr.faa.gov/tfr2/list.jsp). Unfortunately no online repository exists for expired TFRs, stymieing analysis of temporary flight restrictions. The data on active TFRs are conveniently formatted in HTML tables, making the data conducive to using the `readHTMLTable` function provided by R's XML package.

## MIT License
You are free to copy and modify this code as you see fit to collect your own TFR data. If you find a way to improve the dataset, then please submit a pull request!

## Dependencies
* `R`,
* R's `XML` package,
* `wget`, and
* `sed`.

## Running the Scripts
For the first collection of TFR data, you'll need to place `tfrData-export-memory-head.csv` into the working directory specified by the R script and rename the file to `tfrData-export-memory.csv`. 

The scripts can be run manually to compile the active list of TFRs, download their shapefiles, and append them to the records collected in your dataset thus far.

```bash
R CMD BATCH /path/to/scripts/FAA-TFR-scraper.R && /path/to/scripts/shapefile-download.sh
```

These scripts are designed to be run automatically with no need to oversee their operation. For instance, I have these scripts running on an AWS EC2 instance, with a cron task that runs every hour like so:

```bash
0 * * * * R CMD BATCH /home/ec2-user/scripts/FAA-TFR-scraper.R && /home/ec2-user/scripts/shapefile-download.sh
```

I'll write up a blog post on how I set this up at a later date, but this [AWS blog post](https://aws.amazon.com/blogs/big-data/running-r-on-aws/) on how to run R on an EC2 instance was very helpful for me as I got started.

If you do not have an AWS account currently, you can sign up and run an EC2 instance under the "free tier" for 12 months. You can collect TFR data for a year free of charge!

## Why do I need the Shell script?
The FAA posts shapefiles for most TFRs that contain valuable information about the landarea and altitudes affected that R's XML package cannot scrape.

Running the shell script after the R script will loop over the URLs where each active TFRs shapefiles are stored and download them using `wget`. The script in this repo places the shapefiles into directory `/tmp/` and then unpackage the archives in a specified directory. This script is written to be run on an EC2 instance, so modifications will be necessary for other environments.

