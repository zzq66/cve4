NAME OF AFFECTED PRODUCT(S):zzcms
AFFECTED AND/OR FIXED VERSION(S):zzcms2023
PROBLEM TYPE:File Upload; 
Impact:Arbitrary code execution;
DESCRIPTION:ZZCMS 2023 has a file upload vulnerability in 3/E_bak5.1/upload/index.php, allowing attackers to exploit this vulnerability to gain server privileges and execute arbitrary code.



First, download the latest version of zzcms:
![image](https://github.com/zzq66/cve4/assets/68541960/0a1302bf-ad8e-4a6c-baf2-c159baaf70ed)

Login to the admin panel using the default username and password: admin admin.

Click on "Backup Data."
![image](https://github.com/zzq66/cve4/assets/68541960/99daa48d-7246-4605-ba1f-f4b5d33f6b19)

Choose one of the database tables for backup.
![image](https://github.com/zzq66/cve4/assets/68541960/1d671531-4cf4-4c2c-ae2f-8f3c4638c5d1)

Capture the request using Burp Suite.
![image](https://github.com/zzq66/cve4/assets/68541960/a3c01666-68af-4be5-bca4-dfd573053b72)

Modify the "tablename" field to "eval($_POST[1])."
![image](https://github.com/zzq66/cve4/assets/68541960/e66b7119-63b1-41b5-b0b2-fdbc3bbb8b1f)

Click on "View Backup Instructions."
![image](https://github.com/zzq66/cve4/assets/68541960/45c398ca-8257-4401-8a7a-5947523fe3d1)
![image](https://github.com/zzq66/cve4/assets/68541960/285700a1-9894-472b-83a5-80fd73836206)


Change the file name to "config.php" and modify the post parameter content to "1=phpinfo();"

Now, a one-liner PHP webshell is uploaded to the backend.
![image](https://github.com/zzq66/cve4/assets/68541960/5fbe5e85-5c65-4a5b-baa3-d87514b08973)
![image](https://github.com/zzq66/cve4/assets/68541960/5f02f47f-6c89-40ff-adb1-84b9080655c5)

Vulnerability Code Analysis:
The "phonebak.php" file takes $_POST as a parameter, meaning it's user-controllable.
![image](https://github.com/zzq66/cve4/assets/68541960/d0269a18-736d-4155-9515-a79075837ba3)

The "tablename" parameter is passed to the $tablename variable.
![image](https://github.com/zzq66/cve4/assets/68541960/73a442ab-c93e-499d-96ac-0ce60a253dc6)

Later, the tablename is formatted into a string in the form of $tb[".$tablename[$i]."]=0:
![image](https://github.com/zzq66/cve4/assets/68541960/79976f60-aec5-4674-bc96-6cd99427c541)

There is no single quote around "tablename," and no corresponding escaping or filtering operations are performed. This allows the $string to be written into a PHP file along with other content, ultimately leading to Remote Code Execution (RCE).
![image](https://github.com/zzq66/cve4/assets/68541960/b13d48c5-d369-4e10-a4a4-c8d581d6e3d3)

Mitigation Measures:

Disable the backup module.
Implement character filtering and relevant escaping operations on the $tablename variable.
