# county-data

Manager of arbitrary collected data of US counties, or at the
county-year level.

This is a general system, for anyone who has county-level data and
wants to include joint analyses.  The subdirectories in this
repository contain county-level datasets and the logic for exposing
it.

# Installation

The county-data tool requires numpy, pandas, pyyaml, xlrd, and prompt_toolkit.

```
pip install numpy pandas pyyaml xlrd prompt_toolkit
```

It has only been tested with python 2.7.

# How to use the data

Depending on your use-case, there are three main ways to use the
county-data package.

1. If you want to use an individual dataset, you can find the source
   data in the subdirectory.  Since each dataset is in a different
   format, it can be helpful to use the standardized interface to
   extract data from it.  To do so, in python, run the following:
   ```
   import sys
   sys.path.append("<path to county-data>")
   from <database> import main
   db = main.load()
   ```
   where `<database>` is one of the subdirectories.  Methods on the
   `db` object then provide access to the data.  The main methods are:
   
   * `get_variables()`: A list of the available variables.
   * `describe_variable(variable)`: Return a text description of a
     variable.
   * `get_unit(variable)`: Return the canonical unit for variable.
   * `get_fips()`: A list of the avalable counties, as FIPS codes.
   * `get_years()`: A list of the available years (or None for a
     single year).
   * `get_data(variable, year)`: Return an ordered list of data
     values, in the same order as the FIPS codes.

2. If you want to use multiple datasets, it is best to use the export
   tool, which provides an interactive interface to the data.  The
   export tool joins the data across different FIPS orders, and
   exports the data as a CSV.
   
3. If you aren't sure what data is most useful, and want to do some
   data-mining, you can export the most recent year of all the
   variables in all the datasets across all counties.  To do so, run:
   ```
   python -m analysis.alldata
   ```

   The export takes about 1 hour, and produces a file `results.csv`.

# The export tool

The export tool allows variables to be extracted from the datasets,
merged with variables from other datasets, and exported into new
files.  You can run the export tool at the terminal with:
```
python export.py
```

This will then give you a prompt, which includes the following
commands (amongst others, in order of example usage):

 - `help`: Provide a list of commands or help on a particular command
   with `help [command]`.

 - `available`: List the datasets that are avaialable to load.

 - `load [dataset]`: Load a dataset and prepare it for additional processing.

 - `list`: List the variables that are available across all loaded datasets.

 - `add [variable]`: Add one of the available variables to the export file.

 - `export [filename]`: Create a new file with just the added variables.

 - `bye`: Exit.

# Adding new datasets

To include a new CSV file, do the following:

1. Create a new subdirectory and place the data files there.
2. Create an empty `__init__.py` file in that directory.
3. Create a file `main.py` in that directory and include the following:
```
import os
import database

def get_description(variable):
    return "Ask YOURNAME about %s." % variable

def load():
    datapath = os.path.join(os.path.dirname(os.path.realpath(__file__)), "DATAFILE.csv")
    return database.CSVDatabase(datapath, 'FIPS', get_description)
```

Fill in `DATAFILE` with the filename, `FIPS` with the name of the FIPS
code column, and `YOURNAME` with your name.

Currently this returns no useful information about the variables in
the `get_description` function, but you are encouraged to add
variable-specific descriptions.
