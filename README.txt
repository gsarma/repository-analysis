Summary:
Raw data was extracted using the cloc-git shell script found here:

https://stackoverflow.com/questions/26881441/can-you-get-the-number-of-lines-of-code-from-a-github-repository#29012789

Requires installing CLOC (“Count Lines of Code”), can be installed via Homebrew.  

Files:
analysis.nb -> WolframLanguage script for analysis  
OW_code_summary.csv -> Summaries for all repos grouped by language

shell_scripts/OW_repos -> Names of all of the OpenWorm repos
shell_scripts/cloc-git -> Shell script to generate code summary for a single repo
shell_scripts/loop-counts -> Loop cloc-git over list of OpenWorm repos

raw_data/* -> Summary information for all OpenWorm repos