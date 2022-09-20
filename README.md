# Easy WAF 🧱

An easy-to-use Web Application Firewall (WAF) for Node.js.

> ⚠️ This software tries to defend many common attacks while keeping the rate of false positives low. There will always be methods to bypass this WAF. Therefore, using this package is not a reason to neglect security when developing an application.

```javascript
const express = require('express');
const { easyWaf } = require('easy-waf');
const app = express();

app.use(easyWaf());

app.listen(3000);
```

## Features
- Restrict allowed HTTP methods and add your own ip blacklist
- Blocks requests from bad bots
- Blocks malicious requests:
  - CRLF Injection
  - Cross-Site-Scripting (XSS)
  - Directory / Path Traversal
  - Open Redirect / Server Side Request Forgery (SSRF) (redirectUrlWhitelist option must be set)
  - Prototype Pollution
  - SQL Injections and NoSQL Injections
- Can block requests from the Tor network (disabled by default)

## Installation
> ⚠️ I strongly recommend to activate the "dryMode" at the beginning to be able to identify possible false positives from the logs.
If EasyWaf should parse bodies, bind a body-parser middleware to your app before adding EasyWaf.

If you run your Node.js app behind a reverse proxy, don't forget to set the `trustProxy` option.
To enable Open Redirect protection, configure the `redirectUrlWhitelist` option.

In the examples folder you can find examples of how to integrate EasyWaf into your NextJS or NuxtJS application.

## Configuration
EasyWaf is easy to use without the need for much configuration, but there are still many customization options.
```javascript
app.use(easyWaf({
    allowedHTTPMethods: ['GET', 'POST'],
    redirectUrlWhitelist: ['github.com']
    modules: {
        directoryTraversal: {
            enabled: true,
            excludePaths: /^\/exclude$/i
        },
    }
}));
```
| Option             | Type     | Default | Description                                                                                                                                  |
| -----------------  | -------- | ------- |  ------------------------------------------------------------------------------------------------------------------------------------------- |
| allowedHTTPMethods | array    | undefined | List of all HTTP request methods that are allowed. All other request methods will be blocked. By default, all HTTP methods are allowed.      |
| redirectUrlWhitelist | array    | undefined | List of urls that are allowed to be included in the path or query of the request url. By default, all urls are allowed. (Open Redirect) |
| disableLogging     | boolean  | false   | If true, nothing is logged. *This is not recommended!*                                                                                       |
| dryMode            | boolean  | false   | If true, suspicious requests are only logged and not blocked. In addition, the log format is changed to prevent an IPS from blocking the IP. |
| ipBlacklist        | array    | []   | All requests by ips on the blacklist are blocked. |
| modules[name].enabled      | boolean | true, except "Block Tor Exit Nodes"   | This option allows you to completely disable a specific module.                                                                        |
| modules[name].excludePaths | boolean | undefined   | Exclude paths from being checked by this module with a regex.                                                                     |
| trustProxy         | string / array | [] | If a reverse proxy is used, this setting must be configured. See [npm/proxy-addr](https://www.npmjs.com/package/proxy-addr) for possible values.              |

## Modules

The following table shows which user input is checked by a module:

| Name                          | URL | Body | User Agent | Cookies | IP |
| ----------------------------- | --- | ---- | ---------- | ------- | -- |
| Bad Bots                      | ❌  | ❌  | ✅         | ❌     | ❌ |
| Block Tor Exit Nodes          | ❌  | ❌  | ❌         | ❌     | ✅ |
| CRLF Injection                | ✅  | ✅  | ❌         | Planed  | ❌ |
| Cross-Site-Scripting (XSS)    | ✅  | ✅  | ✅         | Planed  | ❌ |
| Directory Traversal           | ✅  | ✅  | ❌         | Planed  | ❌ |
| NoSQL Injections              | ✅  | ✅  | ✅         | Planed  | ❌ |
| Open Redirect / SSRF          | ✅  | ❌  | ❌         | ❌     | ❌ |
| Prototype Pollution           | ✅  | ✅  | ✅         | Planed  | ❌ |
| SQL Injections                | ✅  | ✅  | ✅         | Planed  | ❌ |

## Contributing
Any contribution is greatly appreciated.

A few notes on PRs and code contributions:
- After cloning the repo run `npm i`
- Run `npm run precommit` before every commit: this runs ESLint and TypeScript (this should normally be done automatically by a git hook)
- If you add new modules or other features, create tests
- Before you create a PR, run `npm t` to run all tests

## Contact
If a public GitHub issue or discussion is not the right choice for your concern, you can contact me directly:
- E-Mail: [info@timokoessler.de](mailto:info@timokoessler.de)
- Twitter: [@timokoessler](https://twitter.com/timokoessler)

## Sources
- [Payloads All The Thing: A list of useful payloads and bypass for Web Application Security](https://github.com/swisskyrepo/PayloadsAllTheThings)
- [HackTricks: A free hacking book](https://book.hacktricks.xyz/pentesting-web/)
- [Nginx Ultimate Bad Bot Blocker: The source of the bad bots list for EasyWaf](https://github.com/mitchellkrogza/nginx-ultimate-bad-bot-blocker)