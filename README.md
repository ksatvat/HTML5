# HTML5

## HTML5: Cross-Site Scripting Protection

## Abstract
The X-XSS-Protection header is explicitly disabled which may increase the risk of cross-site scripting attacks.
## Explanation
X-XSS-Protection refers to a header that is automatically enabled in Internet Explorer 8 upwards and the latest versions of Chrome. When the header value is set to false (0) cross-site scripting protection is disabled.

The header can be set in multiple locations and should be checked for both misconfiguration as well as malicious tampering.

## HTML5: MIME Sniffing

### Abstract
The Django application does not set the X-Content-Type-Options to nosniff or explicitly disables this security header.
### Explanation
MIME sniffing is the practice of inspecting the content of a byte stream to attempt to deduce the file format of the data within it.

If MIME sniffing is not explicitly disabled, some browsers can be manipulated into interpreting data in a way that is not intended, allowing for cross-site scripting attacks.

For each page that could contain user-controllable content, you should use the HTTP header X-Content-Type-Options: nosniff.


## HTML5: Misconfigured Content Security Policy

### Abstract
Incorrectly configured Content Security Policy (CSP) could expose an application against client-side threats including cross-site scripting, cross-frame scripting and cross-site request forgery.
### Explanation
Content Security Policy (CSP) is a declarative security header that allows developers to dictate which domains the site is allowed to load content from or initiate connections to when rendered in the web browser. It provides an additional layer of security from critical vulnerabilities such as cross-site scripting, clickjacking, cross-origin access and the like, on top of input validation and checking an allow list in code. An improperly configured header, however, fails to provide this additional layer of security. The policy is defined with the help of fifteen directives including eight that control resource access, namely: script-src, img-src, object-src, style_src, font-src, media-src, frame-src, connect-src.

Each of these takes a source list as a value specifying domains the site is allowed to access for a feature covered by that directive. Developers may use wildcard * to indicate all or part of the source. None of the directives are mandatory. Browsers will either allow all sources for an unlisted directive or will derive its value from the optional default-src directive. Furthermore, the specification for this header has evolved over time. It was implemented as X-Content-Security-Policy in Firefox until version 23 and in IE until version 10, and was implemented as X-Webkit-CSP in Chrome until version 25. Both of the names are deprecated in favor of the now standard name Content Security Policy. Given the number of directives, two deprecated alternate names, and the way multiple occurrences of the same header and repeated directives in a single header are treated, there is a high probability that a developer might misconfigure this header.

Consider the following misconfiguration scenarios:

- Directives with unsafe-inline or unsafe-eval defeats the purpose of CSP.
- script-src directive is set but no script nonce is configured.
- frame-src is set but no sandbox is configured.
- Multiple instances of this header are allowed in same response. A development team and security team might both set a header but one may use one of the deprecated names. While deprecated headers are honored if a header with the latest name (that is, Content Security Policy) is not present, they are ignored if a policy with content-security-header name is present. Older versions only understand deprecated names, hence, in order to achieve desired support it is essential that the response include an identical policy with all three names.
- If a directive is repeated within the same instance of the header, all subsequent occurrences are ignored.

Example 1: The following django-csp configuration uses unsafe-inline and unsafe-eval insecure directives to allow inline scripts and code evaluation:

```
...
MIDDLEWARE_CLASSES = (
    ...
    'csp.middleware.CSPMiddleware',
    ...
)
...
CSP_DEFAULT_SRC = ("'self'", "'unsafe-inline'", "'unsafe-eval'", 'cdn.example.net')
...
```

## HTML5: Overly Permissive Content Security Policy

### Abstract
Content Security Policy (CSP) is configured with an overly permissive policy which can pose security risks.
### Explanation
Content Security Policy (CSP) is a declarative security header that enables developers to specify allowed security-related behavior within the browser, including an allow list of locations from which content can be retrieved. It provides an additional layer of security from critical vulnerabilities such as cross-site scripting, clickjacking, cross-origin access and the like, on top of input validation and checking an allow list in code. An improperly configured header, however, fails to provide this additional layer of security. The policy is defined with the help of 15 directives including 8 that control resource access: script-src, img-src, object-src, style_src, font-src, media-src, frame-src, connect-src. These 8 directives take a source list as a value that specifies domains the site is allowed to access for a feature covered by that directive. Developers can use a wildcard * to indicate all or part of the source. Additional source list keywords such as 'unsafe-inline' and 'unsafe-eval' provide more granular control over script execution but are potentially harmful. None of the directives are mandatory. Browsers either allow all sources for an unlisted directive or derive its value from the optional default-src directive. Furthermore, the specification for this header has evolved over time. It was implemented as X-Content-Security-Policy in Firefox until version 23 and in IE until version 10, and was implemented as X-Webkit-CSP in Chrome until version 25. Both of these names are deprecated in favor of the now standard name Content Security Policy. Given the number of directives, two deprecated alternate names, and the way multiple occurrences of the same header and repeated directives in a single header are treated, there is a high probability that a developer can misconfigure this header.

In this case, a *-src directive has been configured with an overly permissive policy such as *Example 1: The following django-csp setting sets an overly permissive and insecure default-src directive:

```
...
MIDDLEWARE_CLASSES = (
    ...
    'csp.middleware.CSPMiddleware',
    ...
)
...
CSP_DEFAULT_SRC = ("'self'", '*')
...
```

## HTML5: Overly Permissive CORS Policy

### Abstract
The program defines an overly permissive Cross-Origin Resource Sharing (CORS) policy.
### Explanation
Prior to HTML5, Web browsers enforced the Same Origin Policy which ensures that in order for JavaScript to access the contents of a Web page, both the JavaScript and the Web page must originate from the same domain. Without the Same Origin Policy, a malicious website could serve up JavaScript that loads sensitive information from other websites using a client's credentials, cull through it, and communicate it back to the attacker. HTML5 makes it possible for JavaScript to access data across domains if a new HTTP header called Access-Control-Allow-Origin is defined. With this header, a Web server defines which other domains are allowed to access its domain using cross-origin requests. However, exercise caution when defining the header because an overly permissive CORS policy can enable a malicious application to inappropriately communicate with the victim application, which can lead to spoofing, data theft, relay, and other attacks.

Example 1: The following is an example of using a wildcard to programmatically specify to which domains the application is allowed to communicate.

```
response.addHeader("Access-Control-Allow-Origin", "*")
```

Using * as the value of the Access-Control-Allow-Origin header indicates that the application's data is accessible to JavaScript running on any domain.



## HTML5: Unenforced Content Security Policy

### Abstract
A Content Security Policy (CSP) is configured in monitor mode, which means the browser does not enforce it.
### Explanation
A Content Security Policy (CSP) is a declarative security header that enables developers to dictate which domains the site is allowed to load content from or initiate connections to when rendered in the web browser. It provides an additional layer of security from critical vulnerabilities such as cross-site scripting, clickjacking, and cross-origin access, in addition to input validation and checking an allow list in code.

The Content-Security-Policy-Report-Only header provides the capability for web application authors and administrators to monitor security policies, rather than enforce them. This header is typically used when experimenting and/or developing security policies for a site. When a policy is deemed effective, you can enforce it by using the Content-Security-Policy header instead.

Example 1: The following code sets a Content Security Policy to Report-Only mode:
```
    response.content_security_policy_report_only = "*"
```
