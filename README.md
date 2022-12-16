# APHIS Report / License Scraper

This repository aims to collect all of the following from the US Department of Agriculture's Animal and Plant Health Inspection Service [public search tools](https://efile.aphis.usda.gov/PublicSearchTool/s/):

- [Inspection reports](https://efile.aphis.usda.gov/PublicSearchTool/s/inspection-reports). (In progress.)
- All present and past licensees and registrants, from the same link above. (Not yet started.)
    - Note: Active licensees/registrants available in [APHIS spreadsheet](https://www.aphis.usda.gov/animal_welfare/downloads/List-of-Active-Licensees-and-Registrants.xlsx) linked from [public search landing page](https://efile.aphis.usda.gov/PublicSearchTool/s/).
- [Registrants' annual reports](https://efile.aphis.usda.gov/PublicSearchTool/s/annual-reports). (Not yet started)

## Inspection reports

### Fetching the inspection reports

#### Initial fetch

Although it should have been trivial for APHIS to provide a bulk download of the [inspection reports](https://efile.aphis.usda.gov/PublicSearchTool/s/inspection-reports), it does not. Moreover, although the public search tool runs on a publicly-inspectable API, both the tool and the API allows users to view no more than 2,100 results per query.

The script [`scripts/00-fetch-inspection-list.py`](scripts/00-fetch-inspection-list.py) solves these problems by iterating the `customerName` input fragment by fragment (e.g., `aa`, `ab`, `ac`) to assemble the full list, saving the results to [`data/fetched/inspections.csv`](data/fetched/inspections.csv). (Previous attempts including subdividing by state or certification type — with letter-by-letter searching a last resort — but APHIS's options for those search criteria appeared not to be comprehensive, based on the results.)

This approach appears to capture all of the inspection reports, per a comparison of the number of results indicated in the public search tool and the number of rows in [`data/fetched/inspections-latest.csv`](data/fetched/inspections-latest.csv).

Note: The results returned by the tool and API contain no unique inspection ID, so the results are deduplicated based on the full contents of each row.

#### Refreshing the data

The full fetch can take more than an hour, even when using a pool of four simultaneous processing pools. We can avoid refetching everything when refreshing the results by fetching the 2,100 most recent inspection reports (the query tool's default sorting order), which should suffice as long as it's run with some frequency. (As of late 2022, those 2,100 results go back to nearly four months prior.) This process is conducted via [`scripts/01-refresh-inspection-list.py`](scripts/01-refresh-inspection-list.py), which updates the file at [`data/fetched/inspections.csv`](data/fetched/inspections.csv).

Alternatively, to fully refetch the data, delete all files in the [`data/fetched/inspections-by-letter/`](data/fetched/inspections-by-letter/) directory and rerun [`scripts/00-fetch-inspection-list.py`](scripts/00-fetch-inspection-list.py).

### Downloading the inspection reports

The script [`scripts/02-download-inspection-pdfs.py`] downloads all inspection reports in [`data/fetched/inspections.csv`](data/fetched/inspections.csv) to [`pdfs/inspections/`](pdfs/inspections/). Because the data provided by the inspection search tool does not include the official inspection IDs, the filenames use the first 16 characters of the PDF URL's SHA1 hash hexdigest.

## Licensees

__TK - Not yet implemented.__

## Registrants

__TK - Not yet implemented.__

## Annual reports

__TK - Not yet implemented.__

## Running the code yourself

- Ensure you have Python 3 installed.
- From this repository, run `python -m venv venv` to create its virtual environment.
- Run `. venv/bin/activate && pip install -r requirements.txt` to install the necessary Python libraries.
- Consult the [`Makefile`](Makefile) to understand the available `make` commands.

## Questions

File an issue in this repository or email Jeremy Singer-Vine at `jsvine@gmail.com`.
