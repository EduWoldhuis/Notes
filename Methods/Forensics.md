##### Apache logs
Usual locations: 
`/var/log/apache2/access.log`  -> Look for log poisoning, signs of LFI, RFI, RCE, User-Agents
`/var/log/apache2/error.log`    -> Look for file uploads, attempted reverse shells
Make sure to look for POST requests (If "binary file matches", use `--text`)

##### Web server analysis


##### Persistence
Look for:
History files (bash_history)

cron  
Services/systemd
bashrc
Kernel modules
SSH keys
`.profile` files