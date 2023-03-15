# MadDog Memorial

**Category**: Web

**Points**: 350pts

**TL;DR:** SQLi in the /posts/:id endpoint to get the secret key to forge our own admin JWT token. 💾💉🔑🎨

**Sources**:  [sources_web_maddog_memorial.zip](./sources_web_maddog_memorial.zip)

**Solution:**

Checking the source code we noticed that the flag was read from a file and only displayed in the webapp if the user was an admin, which the application determined by comparing the username set in the session JWT token against the "admin" string.

As we noticed in the entrypoint.sh file that the key was just 15 characters long and saved in the DB we initially though about brute-forcing the HS256 JWT token. However, we soon realized that even what is considered a weak key was to strong for us to break. So we moved on to look for other attack vectors. 

<p align="center">
  <img src="../assets/tannen_memorial_keys_and_db.png" alt="tannen_memorial_keys_and_db" />
</p>
<p align="center">JWT key random generation and storage.</p>

We then realized that the ***/posts/:id endpoint was be vulnerable*** as it passed the id parameter to a DB query that lacked prepared statements, parametrization, or sanitation.

<p align="center">
  <img src="../assets/vulnerable_endpoint_parameter.png" alt="vulnerable_endpoint_parameter" />
</p>
<p align="center">Endpoint with the vulnerable parameter.</p>

<p align="center">
  <img src="../assets/unsafe_sql_query.png" alt="unsafe_sql_query" />
</p>
<p align="center">DB query without a prepared statement, parametrization or sanitation.</p>

With that knowledge, we used sqlmap to perform an SQLi in the DB and dump the keystore table, obtain the PRODKEY secret for "kid 2", and with it forged our own admin token.

	sqlmap -r maddog.txt -D tannen_memorial -T keystore -C kid,secret --dump

Keystore table dump: 
| kid | secret          | 
| --- | --------------- |
| 1   | HKB8pmSesIcctQp |
| 2   | 5KQNdtJhiyQeW0h |

<p align="center">
  <img src="../assets/forging_admin_jwt_token.png" alt="forging_admin_jwt_token" />
</p>
<p align="center">Forging an admin JWT token.</p>

Finally, calling the "/" endpoint of the webapp using that session token returned the flag **HTB{t4nn3n_fr4m3d_th3_mcFly}**.

<p align="center">
  <img src="../assets/wanted_flag.png" alt="wanted_flag" />
</p>
<p align="center">The wanted flag displayed in the webapp.</p>
