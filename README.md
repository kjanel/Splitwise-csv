# splitwise-csv [![Build Status](https://travis-ci.org/esecules/splitwise-csv.svg?branch=master)](https://travis-ci.org/esecules/splitwise-csv)
Upload expenses to splitwise from a csv file and splitting them evenly between a predefined group.

## Fast Use
2 default Monthly statement CSV configurations have been detailed so far (Compte-chèque Desjardins, and Desjardins Mastercard).
In order to add a configuration that's likely to be reused often, add another attribute options to the CsvSettings class
```
class CsvSettings():
    def __init__(self, rows):
        print "These are the first two rows of your csv"
        print '\n'.join([str(t) for t in rows[0:2]])
        if raw_input("Transactions from Releve Debit Desjardins? [Y/n]").lower() != 'n':
            self.date_col = 3
            self.amount_col = 7
            self.desc_col = 5
            self.selector_col = 6
            self.percentage_not_to_split_col = 14
            self.has_title_row = True
            self.newest_transaction = ''
```

Columns that need to be specified in this format are the following:
- `self.date_col` : column number (starting with 0) where the date is specified in the CSV. Note that depending on the date format, the variable `csvDateFormat="%Y-%m-%d" ` might need to be changed to be processed accordingly by strftime
- `self.amount_col` : column number where the amount is specified
- `self.desc_col` : column number where the expense description is specified
- `self.selector_col` : column number where there is an "x" for every expense in the CSV that needs to be considered
- `self.percentage_not_to_split_col` : column number where a 0 to 1 percentage is specified if a part of the expense must NOT be split
- `self.has_title_row` : whether the CSV's first row is for titles or not
- `self.local_currency` : currency of all expenses in the CSV (default to "CAD')

Interesting flag: use `-y`when calling in the CLI to automatically accept all the expenses specified with "x" without reviewing one by one in the CLI


## API Keys
You will need to obtain your own API keys from Splitwise.
https://secure.splitwise.com/apps/new
Set the "Callback URL" to "localhost:5000"
Store your consumer key and consumer secret in src/consumer_oauth.json like so:
```
{
    "consumer_key":"YOUR CONSUMER KEY",
    "consumer_secret":"YOUR CONSUMER SECRET"
}	
```

## Prerequesites
`pip install -r setup/requirements.txt`

## Usage
### First Usage
`python groupsplit.py <transactions csv> <splitwise group name>`

You will need a Display to authorize this application by entering your login information in the browser spawned from here.
It will ask you a few questions to determine which columns have the date, amount and description, etc. You can save the answers for future use. Then you go through each transaction one by one marking them to be split (or not). Finally it uploads them to your splitwise.
### Normal Usage
`python groupsplit.py <transactions csv> <splitwise group name>`

It will skip right to going through your transactions one by one, so long as you have agreed to it remembering your csv layout. It will also remember the last transaction you considered and start from the one which follows it.
## Resetting
Just delete any or all of the .pkl (pickeled) files. 
If you start to use a new csv layout you have to delete the csv_settings.pkl file 
