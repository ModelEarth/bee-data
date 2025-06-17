# Bee the Predictor

We forecast using two bee population targets:<br>
1. Top 20% Density - Counties with the highest density of bee colonies based on the size of each county<br>
2. Top 20% Growth - Changes in county bee populations between years.

## Bee Population Density as Target

[**Our Bee Density Colab**](https://colab.research.google.com/drive/1X04_N4E-WpcNRrolB2VgnvMfa8It99ZW?usp=sharing) prepares honeybee population data for use in our [RealityStream ML](../realitystream/). The dataset comes from the [**USDA National Agricultural Statistics Service (NASS) Quick Stats**](https://quickstats.nass.usda.gov/), and geographic data is retrieved from the [**U.S. Census Bureau's GEOINFO API**](https://api.census.gov/data/2023/geoinfo?get=GEO_ID,NAME,AREALAND,AREAWATR&for=county:*).

We use county-level bee population density and classify the top 20% as high-density 

The bee density colab generates a .csv file containing County Fips codes and their binary classification. The Target column is set to 1 for counties with high bee population densities and 0 otherwise.

View .csv output: [bee-data/targets](https://github.com/ModelEarth/bee-data/tree/main/targets)

This dataset was sourced directly from the [**USDA Quick Stats**](https://quickstats.nass.usda.gov/) website and contains **county-level bee population data** for different years(2002,2007,2012,2017,2022) across all states in USA. (See [quick stats export image](img/USDA-bee-download.png) near the bottom of the current page.)

### **Processing Steps**

**1. Data Cleaning**:
   - Entries with missing values or **"(D)"** (undisclosed data) are removed.
   - The **county names** are converted to lowercase for consistency.

**2. Fetching County-Level Land Area Data**:
   - The **U.S. Census Bureau GEOINFO API** is used to retrieve **land area data for all U.S. counties**.
   - County names and state names are extracted and formatted.
   - The **total land area (in km²)** is computed for each county.

**3. Merging Bee Population Data with Geographic Data**:
   - The **bee population dataset (2017 data) is read from**:
     ```
     bees/inputs/target/bees-targets-top-20-percent.csv
     ```
   - This dataset is filtered and merged with the **county land area dataset** using county names as the key.
   - FIPS (Federal Information Processing Standards) codes are retained for location identification.

**4. Bee Population Density Calculation**:
   - Bee population density is computed as:
     ```python
     bee_population_density = bee_population / total_area_km²
     ```
   - This provides a **standardized metric** to compare bee populations across counties.

**5. Creating a Binary Classification Target**:
   - The top **20% of counties with the highest bee population density** are labeled as `1` (high-density), while others are labeled as `0`.
   - This classification can be useful for **predictive modeling** in environmental studies.
     
**6. Main Steps to upload the csv file in GitHub**
   - **Read the file locally**:
   - The CSV file is opened in binary mode and its contents are read.
   - **Base64 encode the file**:
   - GitHub’s API requires files to be sent as base64-encoded strings, so the raw content is encoded accordingly.
   - **Check if the file exists in the repo**:
   - A GET request is sent to check whether the file already exists in the target repository path and branch.
   - **Construct the payload**:
   - A JSON payload is prepared containing the commit message, the encoded file content, and (if updating) the SHA of the existing file.
   - **Send a PUT request to upload or update**:
   - The file is pushed to GitHub using a PUT request — it either creates the file or updates the existing one.
   - Print the result
   - prints a success message and the file URL if the upload was successful, or error details if it failed.

## How to Use

CSV. output is sent directly to GitHub.

**1. Open the Notebook** in Google Colab:
   - [BeeDensityTopCounties Colab Link](https://colab.research.google.com/drive/1X04_N4E-WpcNRrolB2VgnvMfa8It99ZW?usp=sharing)

**2. Run All Code Cells**:
   - The script will automatically load and process the data.

**3. Modify the bee-data**:
   - Adjust parameters in the bee-data dataframe to fetch data for different years or regions.

**4. Save Backup periodically**:
   - Backup as [BeeDensityTopCounties-bkup.ipynb](https://github.com/ThanmayiKarpurapu/bee-data/blob/main/BeeDensityTopCounties-bkup.ipynb)



## Random Forests for Bees

Using county industry changes to predict honey bee populations.

- ["Run Models" with Bees](../industries/)

The following are probably all represented by the Run Models Colab now.

- [Our Input CoLab (Preprocessing)](https://colab.research.google.com/drive/1a8lbM7ceGGnaDe0kc1X0QqrZELsJINpb?usp=sharing)
- [Our Output CoLab](https://colab.research.google.com/drive/1y2A_XOFQrfu0HfXDPt2erg43Kn7Tc7xz?usp=sharing)


- [Colab for creating 2-columns targets](https://colab.research.google.com/drive/11R3nSxPn91yTUBWhANBgdKCX0-YV1Dtk#scrollTo=Y9Un4FVwnxth)

NOTE: bees-targets.csv was a copy of bees-targets-increase2022.csv
bees-targets-top-20-percent.csv is the top 20% of colony density (not change over time). It's a copy of bees-population-usda.csv
bees-targets-top-20-percent.csv is the default when the target path is simply "bees"

Backup and run locally in [models/location-forest](../../models/location-forest/):

   python location-forest-input-bkup.ipynb bees
   python location-forest-output-bkup.ipynb bees


[2-column Target tables](https://github.com/ModelEarth/bee-data/tree/main/targets) containing county Fips.


[Our Run Models colab](../industries/) merges 2-column bee targets data for counties with features data with rows for industries and demographic data for each county.


### Bee Pollinators

<div style="overflow:auto; margin-top:0px; padding-right:50px">

  <div style="font-size:16px">
  <b><span class="yeartext"></span>[Prior change] predicting [future] change at locations or in industry mix</b><br>
  For model training, a "y" column value of 1 indicate locations where [Attribute(s)] that changed in a [prior year] predict a later year.<br><br>
  </div>

  <div style="background:#fff; padding:20px; max-width:600px">
     <img src="https://model.earth/community-forecasting/about/img/random-forest.webp" style="width:100%;"><br>
   </div>

  <div style="display:none;font-size:12pt;line-height:16pt;padding-top:20px">
    Best Params: 
    max depth: 8; <!-- max number of levels in each decision tree -->
    n-estimators: 100 <!-- number of trees in the foreset --><br>

    Accuracy before tuning: 69%.&nbsp;
    Accuracy after tuning: 71%.
  </div>
  
</div>

[Prior Bees Output](../../output/bees/)

Factors that contribute to bee colony decline: nutrition deficiencies, mite infestations, viral diseases, pesticide exposure, temperature.