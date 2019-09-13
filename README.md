# MapSorter
bash one liner for sorting finished local kz maps, should work with most versions of GNU coreutils. Has been tested on Debian, Cygwin, and OSX.


## Requirements
Curl (or wget), on systems without the ```tac``` util (OSX, \*BSD) you may use ```tail -r```.

## Input:
Takes in a plain text file containing the console output from the finished maps option. Should be in the following format:

```
kz_11342, Time: 01:42.58 (PRO), Teleports: 0, Rank: 1/2
kz_11342, Time: 01:44.95 (TP), Teleports: 1, Rank: 1/18
kz_11735, Time: 01:46.82 (TP), Teleports: 4, Rank: 1/21
kz_11735, Time: 01:47.63 (PRO), Teleports: 0, Rank: 1/7
```

## Output
Plaintext CSV file that can be opened in any spreadsheet program. Contains the difficulting rating, as well as any unfinished maps.

## Usage:
In any KZtimer enabled server, paste the console output from the finished maps option into a text file and run the following command in the terminal:

```
curl http://www.kzstats.com/api/map | tac | tr -d "\"," | grep "mapname:\|difficulty:" \
| sed "N;s/mapname://g;s/\n/,/; s/^[ \t]*//" | cat - <(sort -t, -k1,1 -k3 <FILE.txt>) \
| awk -F',' '{b=$2~/(TP)/?",":"";a[$1] = a[$1]","b$2};END{for(i in a)print i""a[i]}'  \
| sed 's/(PRO),,/(PRO),/g' | tr -s " "
```
### example:
```
$ touch mytimes.txt

$ cat "kz_11342, Time: 01:42.58 (PRO), Teleports: 0, Rank: 1/2" >> mytimest.txt

$ curl http://www.kzstats.com/api/map | tac | tr -d "\"," | grep "mapname:\|difficulty:" \
| sed "N;s/mapname://g;s/\n/,/; s/^[ \t]*//" | cat - <(sort -t, -k1,1 -k3 mytimestxt) \
| awk -F',' '{b=$2~/(TP)/?",":"";a[$1] = a[$1]","b$2};END{for(i in a)print i""a[i]}'  \
| sed 's/(PRO),,/(PRO),/g' | tr -s " " >> timeswithtiers.txt

```

