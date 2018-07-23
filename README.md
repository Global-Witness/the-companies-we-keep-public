# The companies we keep

The analysis contained in this Github repositry accompanies a briefing published in June 2018 entitled *The companies we keep*. The briefing looked at the UK's open data register of the real owners of companies and identified issues and gaps in the data and processes around verification that need to be addressed. This repositry contains the original analysis which produced the statistics that appear in the briefing. This is available at [`companies_we_keep_analysis.ipynb`](companies_we_keep_analysis.ipynb), the other Jupyter Notebooks include detail on data processing steps to clean and load the data into the format used for the analysis. The analysis and code was produced in collaboration with [DataKind UK](https://www.datakind.org/chapters/datakind-uk).

# Disclaimer 

The outputs presented in this analysis are not in themselves intended to make any specific allegation of wrongdoing against any individual or company identified.

Glossary
========

**Person of Significant Control** - The real owner of a company, sometimes known as the benefifical owner. This can in some cases be a company (when it is a Relevant Legal Entity - see below), but is usally an individual person.

**Secrecy Jurisdiction** - An alternative to the more widely used 'tax haven' denoting a country that has high levels of corporate opacity. For this analysis we define it as any country with a Financial Secrecy score of 60 or above in the Tax Justice Network Financial Secrecy Index 2018.

**Active company** - A company that appears in the Companies House [free company data product](http://download.companieshouse.gov.uk/en_output.html). This includes companies that are in liquidation and with a proposal to strike off.

**Confirmation statement** - A filing that most UK companies must provide to Companies House confirming basic details about the company for the public record including information on the PSCs for the company and directors. It replaced the annual return in 2016.

**Relevant Legal Entity (RLE)** - A legal entity is relevant if it fulfills one of the criteria for exercising significant control as a PSC and either registers its own PSC or is listed on one of the relevant stock exchanges (i.e. in the European Economic Area, Japan, Israel, Switzerland, USA).

**PSC statement** - A statement filed by a company to Companies House giving a reason as to why a PSC is not being filed.

**PSC record** - A record of the PSC of a company. 

Data
====

Data sources
------------

-   [Companies House free data product](http://download.companieshouse.gov.uk/en_output.html) (1/3/2018) - Snapshot of all active UK companies accurate as of 1 March 2018.

-   [Companies House PSC data](http://download.companieshouse.gov.uk/en_pscdata.html) (1/3/2018) - Persons of Significant Control and PSC statements for all UK companies accurate as of 1 March 2018. Most recent data available here.

-   [Companies House disqualified director data](https://www.gov.uk/search-the-register-of-disqualified-company-directors) (23/2/2018) - Data on directors disqualified by the UK courts and Insolvency Service, downloaded in bulk from [OpenSanctions](https://www.opensanctions.org/).

-   [EveryPolitician.org](http://everypolitician.org/countries.html) (6/3/2018) - Crowdsourced data on politicians from across the world.

-   UK officers data (26/2/2018) - Company officers (e.g. directors, secretaries, partners) of all UK companies sourced from OpenCorporates.com.

-   [Financial Secrecy Index 2018](https://www.financialsecrecyindex.com/) - The Financial Secrecy Index ranks jurisdictions according to their secrecy and the scale of their offshore financial activities. 

-   [Companies House Uniform Resource Identifiers (URI) Customer Guide](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/426891/uniformResourceIdentifiersCustomerGuide.pdf) - PDF from Companies House that describes the prefixes for different company types used in our analysis.

Key fields
----------

The datasets exceed Github size limits, so you will need to download the source data. The URLs for the source data (where publicly available) is given in the load and transform notebook. Using the load and transform notebook provided and you can create a set of cleaned CSV files that you can then analyse using the Jupyter Notebook supplied. These CSV files are created from the raw data using the steps described in the Data Processing section. The key fields of these cleaned CSV files are described below, however there are other fields in each dataset not described here.

-   **active_companies** - All active companies listed by Companies House. Note that the PSC regime does not apply to all companies in the file. Filtering is done in the Jupyter Notebook

	-   *name* - Name of company

	-   *CompanyNumber* - Companies House unique identifier. Note that the prefix to the Company Number denotes the company type which is used to filter the data for companies to which the PSC regime applies. More details on the prefixes can be found [here](https://www.gov.uk/government/uploads/system/uploads/attachment_data/file/426891/uniformResourceIdentifiersCustomerGuide.pdf).

	-   *CompanyCategory* - The category of the company e.g. Public Limited Company. Used in combination with the prefix of the company number to exclude certain companies to which the PSC regime does not apply.

	-   *RegAddress.AddressLine1* - First line of registered address of company.

	-   *RegAddress.PostCode* - Postcode of registered address of company.

	-   *first_and_postcode* - Combination of first RegAddress.AddressLine1 and RegAddress.PostCode

-   **active_psc_records** - Records of PSCs that have been disclosed by companies and exemptions (one per line)

	- *company_number* - Companies House unique identifier of the company that has filed the PSC or has the exemption

	- *name* - Full name of PSC (company name in the case of a company)

	- *name_elements.forename* - First name of PSC (not applicable for companies)

	- *name_elements.surname* - Last name of PSC (not applicable for companies)

	- *kind* -  Entity type of PSC e.g. corporate entity with significant control

	- *nationality* - Nationality of PSC

	- *ceased_on* - Date PSC ceased on

	- *address.address_line_1* - First line of correspondence address of PSC

	- *address.postal_code* - Correspondence address post code of PSC

	- *address.country* - Correspondence address country of PSC

	- *date_of_birth.month* - Month of birth

	- *date_of_birth.year* - Year of birth

	- *month_year_birth* - Month and year of birth combined

	- *secret_base* - A Boolean field denoting whether *coutry_of_residence* or *address.country* is a secrecy jurisdiction

	- *join_id* - A combination of name_elements.forename, name_elements.surname and month_year_birth in order to join on to EveryPolitician.org data

-  **active_psc_statements** - One statement per company per row. A statement gives a reason as to why PSC details are not given as opposed to a PSC record which gives information on the PSC.

	- Fields are same as those of active_psc_companies

	- *statement* - Details of statement given regarding why PSC details are not filed e.g. company has no PSC

 -  **active_psc_controls** - One control type per company to enable analysis.

	-  *company_number* - Companies House unique identifier of the company that has filed the PSC

	- *nature_of_control* - Details of the way in which a PSC controls a company e.g. via ownership of shares

	- *secret_jurisdictions* - All jurisdictions that have a financial secrecy score of 60 or above in the 2018 Financial Secrecy Index.

- **every_politician** - A subset of the [EveryPolitician.org](http://everypolitician.org/) dataset with only those politicians with rich enough information to be matched against Companies House data.

	- *first_name* - First name of politician

	- *last_name* - Last name of politician

	- *country* - Country where individual is a politician

	- *birth_date* - Birth date of politician where available (we only retain details of those politicians where at least month and year of birth is available)

	- *month_year_birth* - Only month and year of birth from birth_date

	- *join_id* - Combination of first_name, last_name and month_year_birth in order to join on to PSC data

- **disqualified_directors** - One disqualified director per row.

	- *first_name* - First name of disqualified director

	- *last_name* - Last name of disqualified director

	- *month_year* - Month and year of birth

	- *join_id* - Combination of first_name, last_name and month_year for joining on PSC and officer data

Data processing
===============

Creation of CSV files for analysis and red flagging
---------------------------------------------------

Data was downloaded from the sources defined above and cleaned. The cleaning steps are defined below and the code is available in the [`companies_we_keep_transform_and_load`](companies_we_keep_transform_and_load.ipynb) Jupyter Notebook:

-   Flattening of PSC data JSON file - The PSC data is available in bulk as JSON file. In order to make this more amenable to analysis we flattened this structure and divided into 3 useful CSV files: active_psc_records (all records of PSCs for companies), psc_statements (all PSC statements e.g. a company filing that they don't have a PSC) and psc_controls (one control type per company per line).

-   Downloading and flattening of Every Politician data - Data from Every Politician is downloaded for each legislature as a JSON file from their site.. These files are then flattened and combined. All records that do not contain a date of birth are removed as they cannot be matched to the other data with any confidence.

-   Addition of a *first_and_postcode* field for active_companies and PSC data - For our analysis we used various calculations that required counting the number of companies, officers or PSCs at a given address. We combined the first line of address and postcode fields to make this step for efficient. It is important to note that there is still a degree of inconsistency within this field as the first line of addresses can be typed in a number of different ways e.g. 31 Middleton Road and 31 Middleton Rd.

-   Normalization of of *country_of_residence* and *address_country* for PSC data - New fields were created for both the country of residence and *address_country* fields in the PSC data in order to normalise them and make them more consistent.

-   Addition of *secret_base* field for PSC and officer data - A Boolean field was created in both the officers and PSC data that denotes whether each PSC or officer is based in a secrecy jurisdiction. A secrecy jurisdiction is defined as a country that secrecy score of 60 or above in the Tax Justice Network Financial Secrecy Index. If either of the *address_country* or *country_of_residence* fields for the PSC data or officer data contained such secret_base = True.

-   Addition of a *join_id* field for every_politician - A field is created to enable joining of the Every Politician data and the officers and PSC data. It is combination of first name, last name and month and year of birth.

All other steps involving the manipulation of the data to arrive at our analysis are given in the Jupyter Notebook.

Loading data into Neo4J
-----------------------

This network database was built in Neo4J, the code is available at [`companies_we_keep_neo4J-load`](companies_we_keep_neo4j_load.ipynb).

The lists below describes the basic model:

Node types:

-   Person e.g. a company officer, PSC or politician

-   Company

-   Statement e.g. "this company has no PSC"

-   Legislature e.g. UK House of Commons

-   Postcode

Edge/relationship types:

-   Controls

-   Officer_of

-   Registered_at

-   Politician_in

-   Probably_same_person_as

A specific Neo4J set of load scripts are available in the Github repository. They operate on the CSV files created by the initial load_and_transform notebook. For those without access to bulk officers data, it is possible to rebuild the graph without officers simply by commenting out/removing all lines relevant to it. Below is a list of issues with the graph to be aware of and could be improved in future iterations:

-   **Deduplication of PSCs** - Companies House does not supply a unique identifier for PSCs, therefore we have used a proxy unique identifier by combining first name, last name, month and year of birth and address. This is a conservative deduplication as many PSCs will use different addresses across different filings.

-   **Deduplication of officers** - Officers do have unique identifiers in Companies House data. Therefore we created a proxy field to help deduplicate officers derived from first name, last name and month and year of birth. This is a less conservative approach than that used for PSCs in part because the same officer for a single company can appear as many different officers with different addresses. It is important to confirm that an officer in the graph is actually an officer of company in the original Companies House database.

-   **Registered addresses and corporate PSCs** - Only active companies in Companies House are associated with postcodes. Other corporate PSCs have address information as a property of the node but are not connected to postcode nodes.

- 	**Corrupt company numbers in PSC data** - In the PSC register leading zeros are occassionally removed from company numbers for UK corporate PSCs. These leads to duplicate companies appearing in the graph.

# Licensing 

All code in this repository is available under the MIT License. All data files are available under the Creative Commons Attribution 4.0 International (CC BY 4.0) license.

# Attribution

This analysis was a collaboration between [DataKind UK](https://www.datakind.org/chapters/datakind-uk) and Global Witness. 

# Questions / Feedback

Contact Sam Leon at sleon@globalwitness.org