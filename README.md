# Unix - Grep


## Accompanying resources
* [Perl Compatible Regular Expression Docs](https://www.pcre.org/current/doc/html/pcre2pattern.html)
* [PCRE Cheatsheet](https://www.debuggex.com/cheatsheet/regex/pcre)


## Daily Event Log

Inside of the data directory there is a file called "transaction_data_daily_event_log_20190129.dat". This file represents a log generated by a device such as a gas pump or a vending machine. Every two hours it initiates an upload of its transaction data. The device logs the time it attempted to initiate a transfer as well as the outcome of each attempt.

Use grep to find all instances where the upload was initiated. 
```
ggrep -n upload_started transaction_data_daily_event_log_20190129.dat 
```

Once you've reviewed these results, repeat the process but this time using the -c flag to determine how many matching occurences were found.
```
ggrep -c upload_started transaction_data_daily_event_log_20190129.dat
```


Use grep to find all instances where the upload was successful. 
```
ggrep -n "upload_complete" transaction_data_daily_event_log_20190129.dat
```

Once you've reviewed these results, determine how many matching occurrences were found. This time instead of using the -c flag, pipe the result to the wc program.
```
ggrep "upload_complete" transaction_data_daily_event_log_20190129.dat | wc -l
```


Use grep to find all instances where the upload failed. Ensure your output displays the line numbers for each match.

```
ggrep -n "upload_failure" transaction_data_daily_event_log_20190129.dat
```

Upon review, we would like to only view failures with error code SYSOFFLINE or WEAKSIGNAL.

```
ggrep "upload_failure\|SYSOFFLIINE\|WEAKSIGNAL" transaction_data_daily_event_log_20190129.da
```


## Dunder Mifflin Paper Co. Users

Inside the data directory, there is a file called "users.csv". This file contains details of users registered to an email subscription service provided by Dunder Mifflin paper company. 

Identify users that have email addresses with six or less characters before the @ symbol where none of these characters are numbers.
```
ggrep -E "\b[a-zA-z]{1,6}@" users.csv
```


Marketing research has shown that the paper business is picking up in the academia space. Corporate has requested a list of all registered users that have an edu emaill address. Use grep to find the appropriate lines and output the results to a file called academia_users.txt.
```
ggrep -E "\.edu" users.csv >> academia_users.txt 
```


Ryan Howard did a poor job and used the CC field rather than the BCC field for the company's email campaign. Dwight received a tip from Jim that there is a registered user who exploided this oversight to poach clients from Dunder Mifflin Paper Co. Dwight has provided us with two pieces of information:
1. The user registed to the service from an IPV4 ip address starting with the numbers 184. 
2. The user's phone number starts with the numbers 38.

Use grep to identify the user with a single regex pattern.
```
If using perl regex with gnu grep....
ggrep -P "184[,\d\.]{1,},38" users.csv

If not, this ridiculous expression accounting for ip address format aslso works...
ggrep -E '184\.[[:digit:]]{1,3}\.[[:digit:]]{1,3}\.[[:digit:]]{1,3},38' users.csv
```


## Dunder Mifflin Is Hiring

After Ryan Howard's demotion for mishandling of sensitive data, Dunder Mifflin is searching for a proper software developer. While they are open to hiring a remote worker, they would like someone who resides within the state of Pennsylvania.

You have been provided with two sample tab delimited files inside the data folder. The sample files are named "candidates_1.txt" and "candidates_2.txt" and they contain information about various individuals who are open to hearing about a new career opportunity. You will continue to receive files like this daily until the position is filled.

Each entry in the provided files will contain the following details about a potential hire:
* id
* first_name
* last_name
* job_title
* city
* state

This regex expression will ultimately be part of an automated data pipeline so we want it to be as hardened as we can reasonably make it. Write a regex expression that meets the following constraints.

* Starting and ending anchors for each line.
  * For each line? Does that mean the I'm going to do multiple regex?  
  * Or just start and end the regex?  ^ starts with,  $ end of line/string
* Each entry will have an id of one or more digits
  * "^\d+"  
* Last names must begin with an upper case letter (a-z).
* Last names may contain any number of characters (a-z). It may also contain spaces, dashes, and hyphens.
  * "[A-Z][a-z A-Z-]+" 
* First names must begin with an upper case letter (a-z).
* First names may contain any number of characters (a-z). It may also contain spaces, dashes, and hyphens.
  * "[A-Z][a-z A-Z-]+" 
* A candidate's current job title must contain the word "Software" and/or "Developer".
  * "([Ss]oftware|[Dd]eveloper|[Ss]oftware[\s][Dd]eveloper)"
* City must not contain any digits.
  * [^\d]
* Each field must be separated by a tab character.
  * \s will cover white space \s?

* So I believe something like this should work...
  * "^\d+\s+[A-Z][a-z A-Z-]+\s[A-Z][a-zA-Z]+\s([Ss]oftware|[Dd]eveloper|[Ss]oftware[\s][Dd]eveloper)[A-Z a-z]+\s[^\d]+\s[A-Z][a-z A-Z-]+$"
  * Can't believe I figured this out, woo hoo.
  * More refined, think a few were missing cause Developer is search word and its last word before space
  * "^\d+\s+[A-Z][a-z A-Z-]+\s[A-Z][a-zA-Z]+\s([A-Z a-z]*[Ss]oftware[A-Z a-z]+|[A-Z a-z]*[Dd]eveloper[A-Z a-z]+|[A-Z a-z]*[Dd]eveloper)\s[^\d]+\s[A-Z][a-z A-Z-]+$"


```
ggrep -P "^\d+\s+[A-Z][a-z A-Z-]+\s[A-Z][a-zA-Z]+\s([A-Z a-z]*[Ss]oftware[A-Z a-z]+|[A-Z a-z]*[Dd]eveloper[A-Z a-z]+|[A-Z a-z]*[Dd]eveloper)\s[^\d]+\s[A-Z][a-z A-Z-]+$" candidates_1.txt candidates_2.txt
```