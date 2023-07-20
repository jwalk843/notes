MISC & Others
=============

### Argon2 cracker


https://github.com/CyberKnight00/Argon2_Cracker

# Argon2 is a key derivation function that was selected as the winner of the Password Hashing 
# Competition in July 2015. It is designed by Alex Biryukov, Daniel Dinu, and Dmitry Khovratovich
# from University of Luxembourg. Argon2 has 3 variants : Argon2d, Argon2i and Argon2id.

./crack_argon2.py -c '< Argon2 Hash >' -w '< Path/To/Wordlist >'

### Lestat


https://github.com/astar-security/Lestat
# Great tool by Astar Security, multiple scripts for extraction, parsing and creating wordlists...

# Wiki is also cool to get informations about the different prerequisites and tools
https://github.com/astar-security/Lestat/wiki


# LesterTheLooter cna be used to get data about cracked passwords
# - the list of the domain groups of each craked account
# - an indicator about whether a cracked account is admin or not
# - the status of each cracked account: ACCOUNT_DISABLED or ACCOUNT_ENABLED
# - the list of all the domain groups compromised through the cracked accounts 
#   (no false positive due to disabled users)
# - a comprehensive set of stats about the passwords cracked
#   (length distribution, charset, robustness evaluation, most frequent pattern, ...)

$ python3 LesterTheLooter.py --priv JOHN_RESULT.txt USERS_INFO.txt
[*] Importing john result from JOHN_RESULT.txt and domain info from USERS_INFO.txt
[!] Line ignored in JOHN_RESULT.txt file (not a valid user:password form): 
[!] Line ignored in JOHN_RESULT.txt file (not a valid user:password form): 124 password hashes cracked, 589 left
[*] Computing groups information
[*] Exporting data to users_compromised.csv and group_compromised.csv
[*] Privileged accounts compromised are listed below:
[+]   disabled     domain admins        n.sarko                  Cecilia<3
[+]   enabled      likely admin         f.maçon                  NOMondial2020!
[+]   enabled      enterprise admins    adm.boloré               Y4tch4life
[+]   enabled      domain admins        e.macron                 Macron2022!!!
[+]   enabled      enterprise admins    b.gates                  VaccineApple!
[+]   disabled     account operators    e.philippe               Prosac2k19

# Get some stats in addition of the CSV files
$ python3 LesterTheLooter.py --stats JOHN_RESULT.txt USERS_INFO.txt

# You can get comprehensive stats if you configured the wordlists :
python3 LesterTheLooter.py --wordlists <PATH_TO_WORDLISTS> --stats JOHN_RESULT.txt USERS_INFO.txt


# PNG charts can be generated from the lestat.csv file
# It is recommended to present the result in a Excel file
python3 GregTheGrapher.py -w charts --transparent lestat.csv 


# Working on Wordlists
https://github.com/astar-security/Lestat/wiki/GetWordlists

# Check for leaked NTLM
# Look for presence of users in the HaveIBeenPwned list:
python3 LesterTheLooter.py --verbose <HASHES_FILE> <HAVEIBEENPWNED_LIST>

# Wordlist generation scripts
https://github.com/astar-security/Lestat/tree/master/wordlists
