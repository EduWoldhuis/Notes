Look for:
Any buttons that send POST requests
- Full URL as parameter in address bar: `http://website.thm/form?server=http://server.website.thm/store`
- Hidden field in form: `<input type="hidden", name="server", value="http://server.website.thm/store>"`
- Partial URL, such as the hostname: `http://website.thm/form?server=api` 
- Path of the URL: `http://website/thm/form?dst=/forms/concat`
Blind SSRF: http://requestbin.com